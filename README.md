# Repro for issue 9259

## Versions

firebase-tools: v14.18.0

## Steps to reproduce

1. Download `firebase-tools` v14.7.0
   - `npm i -g firebase-tools@14.7.0`
2. Run `firebase emulators:start --import=./emulator-data_14.7.0 --export-on-exit --project demo-project`
   - wait for emulators to run, then exit it.

<details>
    <summary>debug logs - no errors</summary>

```
$ firebase emulators:start --import=./emulator-data_14.7.0 --export-on-exit --project demo-project
i  emulators: Starting emulators: dataconnect
i  emulators: Detected demo project ID "demo-project", emulated services will use a demo configuration and attempts to access non-emulated services for this project will fail.
⚠  dataconnect: 'firebase.json#emulators.dataconnect.dataDir' is set and `--import` flag was passed. This will overwrite any data saved from previous runs.
✔ Do you wish to continue and overwrite data in dataconnect/.dataconnect/pgliteData? Yes
i  dataconnect: Importing data from /Users/user/Desktop/firebase-tools/issues/9259/emulator-data_14.7.0/dataconnect_export
i  dataconnect: Data Connect Emulator logging to dataconnect-debug.log
i  dataconnect: Started up Postgres server, listening on {"address":"127.0.0.1","family":"IPv4","port":5432}
⚠  emulators: The Emulator UI is not starting, either because none of the running emulators have a UI component or the Emulator UI cannot determine the Project ID. Pass the --project flag to specify a project.

┌─────────────────────────────────────────────────────────────┐
│ ✔  All emulators ready! It is now safe to connect your app. │
└─────────────────────────────────────────────────────────────┘

┌──────────────┬────────────────┐
│ Emulator     │ Host:Port      │
├──────────────┼────────────────┤
│ Data Connect │ 127.0.0.1:9399 │
└──────────────┴────────────────┘
  Emulator Hub host: 127.0.0.1 port: 4400
  Other reserved ports: 4500

Issues? Report them at https://github.com/firebase/firebase-tools/issues and attach the *-debug.log files.

^C
i  emulators: Received SIGINT (Ctrl-C) for the first time. Starting a clean shutdown.
i  emulators: Please wait for a clean shutdown or send the SIGINT (Ctrl-C) signal again to stop right now.
i  Automatically exporting data using --export-on-exit "./emulator-data_14.7.0" please wait for the export to finish...
i  Found running emulator hub for project demo-project at http://127.0.0.1:4400
i  Exporting data to: /Users/user/Desktop/firebase-tools/issues/9259/emulator-data_14.7.0
i  emulators: Received export request. Exporting data to /Users/user/Desktop/firebase-tools/issues/9259/emulator-data_14.7.0.
✔  emulators: Export complete.
✔  Export complete
i  emulators: Shutting down emulators.
i  dataconnect: Stopping Data Connect Emulator
⚠  Data Connect Emulator has exited upon receiving signal: SIGINT
i  hub: Stopping emulator hub
i  logging: Stopping Logging Emulator
```

</details>

3. Download `firebase-tools` v14.18.0

   - `npm i -g firebase-tools@14.18.0`

4. Run `firebase emulators:start --import=./emulator-data_14.7.0 --export-on-exit --project demo-project`

<details>
    <summary>debug logs - errors</summary>

```
$ firebase --version
14.18.0
$ firebase emulators:start --import=./emulator-data_14.7.0 --export-on-exit --project demo-project
i  emulators: Starting emulators: dataconnect
i  emulators: Detected demo project ID "demo-project", emulated services will use a demo configuration and attempts to access non-emulated services for this project will fail.
⚠  dataconnect: 'firebase.json#emulators.dataconnect.dataDir' is set and `--import` flag was passed. This will overwrite any data saved from previous runs.
✔ Do you wish to continue and overwrite data in dataconnect/.dataconnect/pgliteData? Yes
i  dataconnect: Importing data from /Users/user/Desktop/firebase-tools/issues/9259/emulator-data_14.7.0/dataconnect_export
i  dataconnect: downloading dataconnect-emulator-2.14.0...
Progress: =========================================================================================================================================================> (100% of 30MB)
i  dataconnect: Removing outdated emulator files: dataconnect-emulator-2.7.0
i  dataconnect: Data Connect Emulator logging to dataconnect-debug.log
i  dataconnect: Started up Postgres server, listening on {"address":"127.0.0.1","family":"IPv4","port":5432}
Detected a Postgres 16 data directory from an older version of firebase-tools. Migrating to Postgres 17...
Opening database with Postgres 16...
Error: Database already exists, cannot load from tarball
    at mr.vs (/usr/local/lib/node_modules/firebase-tools/node_modules/pglite-2/dist/index.cjs:12:243301)
    at async PostgresServer.migrateDb (/usr/local/lib/node_modules/firebase-tools/lib/emulator/dataconnect/pgliteServer.js:136:9)
    at async PostgresServer.getDb (/usr/local/lib/node_modules/firebase-tools/lib/emulator/dataconnect/pgliteServer.js:107:23)
⬢  dataconnect: Postgres threw an unexpected error, shutting down the Data Connect emulator: AbortError: The operation was aborted
i  emulators: Shutting down emulators.
i  dataconnect: Stopping Data Connect Emulator
⚠  Data Connect Emulator has exited upon receiving signal: SIGINT
i  hub: Stopping emulator hub
i  emulators: Shutting down emulators.

Error: Data Connect emulator: undefined

```

</details>

# Notes

It seems like importing also breaks when downgrading `firebase-tools`

On `firebase-tools` v14.8.0, running `firebase emulators:start --project demo-project --import ./emulator-data_14.18.0/` works without any issues

On `firbease-tools` v14.7.0, running `firebase emulators:start --project demo-project --import ./emulator-data_14.18.0/` error is raised

```

$ firebase --version
14.7.0
$ firebase emulators:start --project demo-project --import ./emulator-data_14.18.0/
i emulators: Starting emulators: dataconnect
i emulators: Detected demo project ID "demo-project", emulated services will use a demo configuration and attempts to access non-emulated services for this project will fail.
⚠ dataconnect: 'firebase.json#emulators.dataconnect.dataDir' is set and `--import` flag was passed. This will overwrite any data saved from previous runs.
✔ Do you wish to continue and overwrite data in dataconnect/.dataconnect/pgliteData? Yes
i dataconnect: Importing data from /Users/user/Desktop/firebase-tools/issues/9259/emulator-data_14.18.0/dataconnect_export
i dataconnect: downloading dataconnect-emulator-2.7.0...
Progress: =========================================================================================================================================================> (100% of 28MB)
i dataconnect: Removing outdated emulator files: dataconnect-emulator-2.7.1
i dataconnect: Data Connect Emulator logging to dataconnect-debug.log
i dataconnect: Started up Postgres server, listening on {"address":"127.0.0.1","family":"IPv4","port":5432}
RuntimeError: unreachable
at wasm://wasm/01edd1ba:wasm-function[3611]:0x262a57
at wasm://wasm/01edd1ba:wasm-function[3610]:0x2627f9
at wasm://wasm/01edd1ba:wasm-function[7801]:0x3e3502
at wasm://wasm/01edd1ba:wasm-function[2533]:0x1c1034
at Module.\_pg_initdb (/usr/local/lib/node_modules/firebase-tools/node_modules/@electric-sql/pglite/dist/index.cjs:12:124284)
at mr.vs (/usr/local/lib/node_modules/firebase-tools/node_modules/@electric-sql/pglite/dist/index.cjs:12:243678)
at process.processTicksAndRejections (node:internal/process/task_queues:105:5)
at async mr.create (/usr/local/lib/node_modules/firebase-tools/node_modules/@electric-sql/pglite/dist/index.cjs:12:238173)
at async PostgresServer.forceCreateDB (/usr/local/lib/node_modules/firebase-tools/lib/emulator/dataconnect/pgliteServer.js:124:28)
at async PostgresServer.getDb (/usr/local/lib/node_modules/firebase-tools/lib/emulator/dataconnect/pgliteServer.js:101:23)
⬢ dataconnect: Postgres threw an unexpected error, shutting down the Data Connect emulator: AbortError: The operation was aborted
i emulators: Shutting down emulators.
i dataconnect: Stopping Data Connect Emulator
⚠ Data Connect Emulator has exited upon receiving signal: SIGINT
i hub: Stopping emulator hub
i emulators: Shutting down emulators.

Error: Data Connect emulator: undefined

```
