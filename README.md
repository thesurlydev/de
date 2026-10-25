# desktop-entry

Create desktop entries for a webpage, making them available via launcher.

`desktop-entry` is a small command line tool that turns a URL into a
freedesktop.org desktop entry, so the page shows up in your application
launcher and can be opened like any other app.

## Target platform

Linux only. The tool writes to `/usr/share/applications` and produces
`.desktop` files that follow the
[freedesktop.org Desktop Entry specification](https://specifications.freedesktop.org/desktop-entry-spec/latest/),
so it works on Linux desktops such as GNOME, KDE Plasma, XFCE, Cinnamon, and
MATE.

macOS and Windows are not supported. Release builds are still produced for
those platforms, but they cannot create working launchers.

## Requirements

- Linux with a freedesktop.org-compliant desktop environment
- Google Chrome installed and available as `google-chrome` on `PATH`
- Permission to write to `/usr/share/applications`; the tool performs the move
  with `sudo`

## Install

From crates.io:

```sh
cargo install desktop-entry
```

Or download a binary from the
[releases page](https://github.com/thesurlydev/de/releases) and put it on your
`PATH`. The `de-linux` asset is the one for Linux.

## Usage

```sh
desktop-entry <name> <link>
```

For example:

```sh
desktop-entry GitHub github.com
```

This creates `/usr/share/applications/GitHub.desktop` with an entry that opens
`https://github.com` in Google Chrome. Because the destination is a system
directory, the file is moved there with `sudo`.

If the link does not start with `http`, `https://` is prepended. Both
`example.com` and `https://example.com` work.

The generated entry looks like this:

```ini
[Desktop Entry]
Name=GitHub
Comment="GitHub website"
Exec=google-chrome https://github.com
Icon=google-chrome
Terminal=false
Type=Application
```

Once written, the entry should appear in your launcher under the given name.
Some launchers cache their menu and may need a refresh or a new login session.

## How it works

1. Builds the desktop entry from the name and link arguments.
2. Writes it to `/tmp/<name>.desktop`.
3. Moves it to `/usr/share/applications/<name>.desktop` with `sudo`.

## Limitations

- Chrome is hardcoded as the browser. Other browsers are not supported yet.
- The name is used verbatim as the file name, so it must not contain `/`.
  Spaces are fine.
- If an entry with the same name already exists, it is overwritten.
- The entry is created system-wide, not per user.
- The output of `sudo` is captured, and a failed move is not surfaced, so the
  command can exit successfully without creating the entry. Check
  `/usr/share/applications` if the launcher does not appear.

## Development

The project uses [mise](https://mise.jdx.dev/) for the toolchain and common
tasks:

```sh
mise run build   # cargo build --release
mise run test    # cargo test
mise run release # tag and push a release, then CI builds binaries and publishes the crate
```

## License

MIT. See [LICENSE](LICENSE).
