Linux Maintenance

Version **1.1.0**. Formerly VibeStation Maintenance. This renamed release includes
both `.deb` and `.rpm` packages and a GitHub-ready source archive.

A small Python 3 / GTK 3 app for an HP Spectre x360 running Zorin OS, with six
read-only maintenance checks. It also works on other Linux laptops. No background
monitor, startup service, telemetry, or automatic checks. MIT licensed.

## Debian package (.deb)

The release package is `dist/linux-maintenance_1.1.0-1_all.deb`. It supports
Zorin OS 17/18 and Ubuntu 22.04/24.04 with Python 3.10+ and GTK 3.22+; its
`Architecture: all` payload is Python source and an SVG icon, without compiled
architecture-specific code. Runtime dependencies are declared in the package.

To install, open the `.deb` in Zorin's graphical package installer and review the
package details and dependencies before selecting **Install**. The package manager
may request administrator authorization because this installs system-wide files.
The app itself always runs as your normal user. No installation happens during
building or application startup. Choose either the `.deb` or the per-user install
below to avoid competing app-menu entries; remove a previous per-user installation
before switching to the `.deb`.

Installed files: `/usr/bin/linux-maintenance`, Python files in
`/usr/share/linux-maintenance`, an app-menu entry in `/usr/share/applications`,
an SVG icon in `/usr/share/icons/hicolor/scalable/apps`, and documentation in
`/usr/share/doc/linux-maintenance`. There are no package maintainer scripts,
startup services, app configuration files, or bundled dependency installers.
Your package manager may run its standard desktop/icon refresh triggers.

To uninstall the `.deb`, find **Linux Maintenance** in the graphical package
manager (package name `linux-maintenance`) and select **Remove** after reviewing
its changes. You can also reopen the installed `.deb` in Software to view its
installed state. Saved diagnostic reports
remain in the locations you chose. `install.py --uninstall` only handles the
separate per-user installation; it cannot remove the system-wide `.deb`.

### Build and inspect without installing

On Zorin/Ubuntu, use Python and the existing `dpkg-deb` command from the `dpkg`
package. No root account, sudo, pip, or downloads are needed:

```bash
python3 -B build_deb.py
dpkg-deb --info dist/linux-maintenance_1.1.0-1_all.deb
dpkg-deb --contents dist/linux-maintenance_1.1.0-1_all.deb
```

The builder creates a `.deb`, a curated source ZIP, and `SHA256SUMS` in `dist/`.
It refuses to overwrite existing artifacts; rebuild into a fresh location with
`python3 -B build_deb.py --output-dir dist/rebuilt`. Compression uses one thread.
The source ZIP includes only named project files, not personal reports or git data.
Verify the checksums from inside `dist/` with `sha256sum -c SHA256SUMS`.

See **[GITHUB.md](GITHUB.md)** for repository setup and release uploads. The included
GitHub Actions workflow tests and builds artifacts; release publication stays manual.

## RPM package (.rpm)

`dist/linux-maintenance-1.1.0-1.noarch.rpm` is for Fedora and compatible RPM systems
with Python 3.10+, `python3-gobject`, and GTK 3.22+. These dependencies are declared
in the RPM; no runtime or native extensions are bundled. It uses the same `/usr`
file layout, application name, and safety behaviour as the `.deb`. It has no
install/removal scripts or startup services. Package-manager desktop refresh
triggers are controlled by the distribution.

**Use the `.deb` on your Zorin laptop.** The RPM is a separate distribution format;
do not install it on Zorin. On Fedora, open the RPM with the graphical package
installer, review dependencies, and confirm installation. To remove it, use the
graphical package manager to remove package `linux-maintenance`; saved reports
remain. System package operations can request administrator authorization. The
application never requests administrator privileges.

Disk, RAM/swap, battery, home scans, and diagnostic reports use Linux interfaces.
The update button currently reads APT lists only: **on systems without APT it
reports unavailable**, and does not invoke DNF, YUM, or Zypper. Review updates
separately in your distribution's software manager. RPM metadata and digests are
tested; installation on a Fedora machine has not been tested here.

### Build RPM or both formats

With an existing native `rpmbuild` toolchain:

```bash
python3 -B build_rpm.py
# Or both formats when dpkg-deb is also available:
python3 -B build_packages.py
```

For a rootless build on Zorin OS 18 / Ubuntu 24.04 amd64, the optional helper first
previews the tool packages and paths. Download/extract them only when you choose:

```bash
python3 -B prepare_rpm_tools.py
python3 -B prepare_rpm_tools.py --download
python3 -B build_packages.py --rpm-tools build/rpm-tools/extracted
```

The helper downloads about 0.75 MB of Ubuntu tools using existing APT lists and
extracts them under `build/rpm-tools`. It never installs a package, refreshes
package lists, or writes to system directories. It requires existing standard
Ubuntu runtime libraries. On other distributions use the native RPM toolchain.
The builder uses temporary build roots and refuses to overwrite existing release
artifacts. For another build, pass a new `--output-dir` to the builder.

Inspect an RPM without installing it with `rpm -qpi FILE.rpm`, `rpm -qpl FILE.rpm`,
`rpm -qp --requires FILE.rpm`, `rpm -qp --scripts FILE.rpm`, and
`rpm -K --nosignature FILE.rpm`. RPMs are unsigned local packages, with digests and
SHA-256 checksums for integrity; no signing identity is claimed.

## Moving from VibeStation Maintenance

The renamed package has new launchers, application IDs, and install paths. Close
the old app and use its previous uninstall instructions if you want to remove it.
The new package does not remove the old application automatically. Your existing
reports remain untouched. The previous source project and release files are also
preserved separately.

## Run without installing

Open a terminal in this project folder and run as your normal desktop user:

```bash
python3 -B app.py
```

Requires Python **3.10+**, GTK **3.22+**, and PyGObject. Check the GTK dependency:

```bash
python3 -B -c "import gi; gi.require_version('Gtk', '3.0'); from gi.repository import Gtk; print('GTK', Gtk.get_major_version(), Gtk.get_minor_version())"
```

Zorin's standard GNOME desktop commonly has these components. If the check fails,
the distribution packages are `python3-gi` and `gir1.2-gtk-3.0`; use Zorin's
graphical package manager to review and install them yourself. The app and its
installer never install dependencies or request administrator privileges.
No pip dependencies are required. Use the distribution's Python interpreter
(usually `/usr/bin/python3`) rather than a virtual environment without GI bindings.

## Optional installation into your account

```bash
python3 -B install.py
```

The installer first lists every destination and describes its changes. Type
`yes` only after reviewing the plan. It copies the application to
`~/.local/share/linux-maintenance`, creates a launcher at
`~/.local/bin/linux-maintenance`, and creates an app-menu entry at
`~/.local/share/applications/io.linuxmaintenance.Maintenance.desktop`. Missing parent
directories are created. There is no system-wide installation and no sudo command.
The installer refuses existing destinations and redirected/symlink paths; use the
portable app if your `.local` directories use symlinks.

Launch from the app menu, or:

```bash
~/.local/bin/linux-maintenance
```

If your menu has not refreshed, sign out and back in. Installation is optional;
it is not performed when you run the application. To replace an installed version,
close it, uninstall it, then install the new source version.

## What each button does

Every check first opens a preview dialog explaining exactly what it reads.
Choose **Run read-only check** to proceed; **Cancel** closes the preview.

| Button | Scope and limits |
| --- | --- |
| Check system updates | Runs `/usr/bin/apt -o Dir::Cache::pkgcache= -o Dir::Cache::srcpkgcache= list --upgradable`. Reads cached APT package lists; disables package-cache generation. Stops after 30 seconds or 512 KiB of output. No network refresh or installation. |
| Show disk space | Reads filesystem statistics for `/` and your home. Displays total, used, and user-available space; these locations may share one filesystem. |
| Inspect RAM and swap | Reads `/proc/meminfo` and `/proc/swaps`. Shows total/available RAM, estimated RAM in use, and swap devices/usage. Includes zram when the kernel exposes it. |
| Report battery health | Reads batteries in `/sys/class/power_supply`. Shows charge, status, cycles where available, and full-charge/design capacity ratio. Uses matching energy or charge units; missing readings stay unavailable. |
| Find large home files | Scans your home, including hidden folders, without reading file contents. Shows up to 30 regular files at least 100 MiB by logical size. Skips symlinks, other filesystems, and directories beyond depth 64. Stops at 120 seconds; Cancel stops at the next iteration. Partial results are clearly labelled. |
| Generate diagnostic report | Builds an in-memory report with OS/kernel/CPU/laptop model and fresh readings of disk, RAM/swap, battery, and cached APT updates. Does not include a home scan, logs, serial numbers, hostname, or network addresses. |

**Update results may be stale.** The app cannot determine current online update
availability without refreshing package lists, which would change local files.
The APT success stamp, if present, is only context and does not prove all sources
were refreshed. This check excludes Flatpak, Snap, and firmware. Review current
updates in Zorin's own Software Updater when you choose to maintain the system.
The app does not open or control that updater.

Battery health is a firmware estimate, not a load test; a reading above 100% can
occur after calibration. Charge percentage is separate from health. The scan uses
logical file sizes, so sparse files may occupy less disk space; hard-linked paths
can appear more than once. Inaccessible, changed, and depth-limited entries are
counted. Other-filesystem entries are intentionally outside the scope.

## Saving a diagnostic report

Generate a report, review its text, and click **Save report…**. Choose a new local
filename. A second dialog shows the exact path, byte count, permissions, and
privacy notice. Only **Create report** writes it. Existing files and symlinks are
never overwritten. New files have private `0600` permissions (or stricter under
your umask). No folders are created by saving. A disk-full or I/O failure can
leave a partial new report; the app explains this rather than deleting it.

Reports can contain your home path, swap paths, hardware model, and package
versions. Review them before sharing. A cancelled report may contain only some
sections and is labelled partial. Running another check replaces the current
result and discards an unsaved report. There is no automatic upload or clipboard
copy, and no automatic report file.

## Resource use and safety

Designed for an 8 GB laptop: GTK and the Python standard library, one worker
thread, and at most one APT child process. File scanning streams directory
entries, retains just 30 results, and limits depth, duration, and open directory
handles. It briefly yields every 256 entries. The UI stays responsive while a
check runs. Scan operations are cancellable between metadata reads; a slow or
stalled filesystem read can delay cancellation. Snapshot checks usually finish
immediately. APT cancellation terminates only the app's own read-only child.

No `sudo`, `pkexec`, package installs, deletions, cache cleanup, disk repair,
swap changes, or power-setting changes exist in the app. It refuses to launch
as root. Python bytecode writing is disabled. Reading directories may update
access timestamps according to the filesystem's mount policy, as with other
read-only tools. The only intentional persistent app write is a report after
confirmation. The optional installer/uninstaller separately requires confirmation.

## Uninstall

Close the app, then run either from this source folder:

```bash
python3 -B install.py --uninstall
```

Or use the installed copy:

```bash
python3 -B ~/.local/share/linux-maintenance/install.py --uninstall
```

Review the deletion list and type `yes` to confirm. Only the six known installed
source/icon/license files, launcher, and app-menu entry are removed. The app
directory is removed only if empty; shared `.local` directories remain. Saved
reports, unknown files, and this source project are preserved. To stop using the
portable app, simply close it; no installation exists to undo. You may remove
the source project manually when you no longer need it.

## Verification

```bash
python3 -B -m unittest discover -s tests -v
python3 -B tests/gtk_smoke.py
```

The smoke check requires GTK and a desktop display; it does not install anything.
Report-save tests write only temporary fixtures. Tests use temporary fixtures,
including sparse large files, and cover
capacity-unit matching, missing data, scan limits/cancellation/symlinks, and APT
failure handling. The smoke check builds the actual window and performs a
read-only RAM check and verifies save cancellation, private permissions, and
overwrite refusal. Firmware estimates still depend on what your Spectre reports.

References: [GTK Python bindings](https://www.gtk.org/docs/language-bindings/python),
[GTK native file chooser](https://docs.gtk.org/gtk3/class.FileChooserNative.html),
[Ubuntu's APT package-management documentation](https://ubuntu.com/server/docs/tutorial/managing-software/),
[Zorin's .deb installation instructions](https://help.zorin.com/docs/apps-games/install-apps/).
