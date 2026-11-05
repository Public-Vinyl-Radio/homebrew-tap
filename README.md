# homebrew-tap

Homebrew formulae for [Groovenet](https://github.com/Public-Vinyl-Radio/groovenet).

## groovenet-now-playing

Shows Groovenet playback in macOS Now Playing and routes the media keys
(play/pause, next, previous) to the player. macOS 13 or later; no Xcode needed.

### Install

```sh
brew install public-vinyl-radio/tap/groovenet-now-playing
brew services start groovenet-now-playing   # now and at every login
```

It follows the local Groovenet player daemon by default. To follow a remote
player, set its ID with the Groovenet CLI, which also holds the API settings
remote mode uses, then restart the service:

```sh
npm i -g @groovenet/cli
groovenet config set now_playing_target <player-id>
brew services restart groovenet-now-playing
```

`groovenet-now-playing --remote <player-id>` or `--socket <path>` runs it once
by hand with a different target.

### Upgrade

```sh
brew upgrade groovenet-now-playing
brew services restart groovenet-now-playing
```

### Uninstall

```sh
brew services stop groovenet-now-playing
brew uninstall groovenet-now-playing
brew untap public-vinyl-radio/tap   # if nothing else uses the tap
```

### Logs

The service logs to `$(brew --prefix)/var/log/groovenet-now-playing.log`
(`/opt/homebrew/var/log/…` on Apple silicon, `/usr/local/var/log/…` on Intel).
`brew services info groovenet-now-playing` shows whether it is running. It
restarts the helper whenever it exits, 30 seconds apart, so a stopped daemon
shows up as a repeated connection error in the log.

### Don't run both

The npm package has its own login item, `groovenet now-playing enable`. Use one
or the other: the Homebrew helper refuses to start while
`~/Library/LaunchAgents/radio.publicvinyl.groovenet.nowplaying.plist` exists, and
`groovenet now-playing enable` refuses while the Homebrew service is installed.
Run `groovenet now-playing disable` before `brew services start`.

### How it is built

The formula installs `@groovenet/player` from npm with Homebrew's `node`, and
the prebuilt, ad-hoc signed `Groovenet Now Playing.app` from the matching
[player release](https://github.com/Public-Vinyl-Radio/groovenet/releases). The
groovenet release workflow updates the formula after each player release and
pushes it here; see
[`NOW_PLAYING.md`](https://github.com/Public-Vinyl-Radio/groovenet/blob/main/packages/groovenet-player/NOW_PLAYING.md).
A release whose npm dependencies are under a day old arrives a day later,
because Homebrew installs npm dependencies with `--min-release-age=1`.
