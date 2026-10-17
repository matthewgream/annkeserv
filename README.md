# annkeserv

Browses the camera archive over HTTP, for the house: a selector of cameras at the top, a calendar of
the days each camera has (newest first), and per day the day's clips in time order with their
length and size. The public wide view stays on the weather archiver (port 80); this one is internal.

Pages follow the weather archiver's snapshot pages (`/opt/weather/server/http`) in look and layout,
without its dependencies: plain `node:http`, no npm packages.

| | |
|---|---|
| `annkeserv` | the server (Node, no npm deps) |
| `annkeserv-storage.service` | gate: it needs `opt-storage-disk.mount` |

## Install

```sh
make test         # run it in the foreground from here, on the configured port
make install      # server, config, storage gate, service
make status       # service state, last log lines
```

Config goes to `/etc/default/annkeserv` (mode 600: it holds the password). A per-host
`annkeserv.<hostname>.cfg` is used instead of `annkeserv.cfg` when present.

## Layout

    /                           -> /<first camera>/list
    /<camera>/list              calendar of the camera's days
    /<camera>/list/<YYYYMMDD>   the day's clips, oldest first
    /<camera>/file/<file>       a file, served with byte ranges (video seeks)

Each `camera-<name>=<directory>` is one of two shapes, told apart by what is in it:

- day subdirectories, `<YYYYMMDD>/recording_<YYYYMMDDHHMMSS>.mp4` (as annkepull writes): a day links
  to its day page;
- top-level files with the date in the name, `timelapse_<YYYYMMDD>.mp4`: a day links straight to the
  file.

Clip lengths are read from each mp4's movie header (near the start, as annkepull writes with
`+faststart`) and remembered while the file is unchanged.

Login is HTTP basic, as the weather archiver: blank user, the configured password. An empty
`password=` turns the login off.

## License

CC BY-NC-SA 4.0 — see [LICENSE](LICENSE).
