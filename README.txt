Use man pages to check command line arguments in configuration:
	man thermald
	man thermal-conf.xml

Prerequisites:
	Kernel
		Prefers kernel with
			Intel RAPL power capping driver : Available from Linux kernel 3.13.rc1
			Intel P State driver (Available in Linux kernel stable release)
			Intel Power clamp driver (Available in Linux kernel stable release)
			Intel INT340X drivers
			Intel RAPL-mmio power capping driver: Available from 5.3-rc1

Companion tools
	ThermalMonitor
		Graphical front end for monitoring and control.
		Source code is as part of tools folder in this git repository.
	dptfxtract
		Download from: https://github.com/intel/dptfxtract
		This generates configuration files for thermald on some systems.

Building and executing on Fedora
1.
Install

	yum install automake
	yum install gcc
	yum install gcc-c++
	yum install glib-devel
	yum install dbus-glib-devel
	yum install libxml2-devel

2
Build

	./autogen.sh
	 ./configure prefix=/
	make
	sudo make install

3
- start service
	sudo systemctl start thermald.service
- Get status
	sudo systemctl status thermald.service
- Stop service
	sudo systemctl stop thermald.service

4. Terminate using DBUS I/F
	sudo test/test_pref.sh
		and select "TERMINATE" choice.



Building on Ubuntu
1. Install
	sudo apt-get install autoconf
	sudo apt-get install g++
	sudo apt-get install libglib2.0-dev
	sudo apt-get install libdbus-1-dev
	sudo apt-get install libdbus-glib-1-dev
	sudo apt-get install libxml2-dev

2
Build

	./autogen.sh
	 ./configure prefix=/
	make
	sudo make install
(It will give error for systemd configuration, but ignore)

3.
If using systemd, use
- start service
	sudo systemctl start thermald.service
- Get status
	sudo systemctl status thermald.service
- Stop service
	sudo systemctl stop thermald.service

Building and executing on openSUSE
1.
Install
	zypper in automake
	zypper in gcc
	zypper in gcc-c++
	zypper in glib2-devel
	zypper in dbus-1-glib-devel
	zypper in libxml2-devel

For build, follow the same procedure as Fedora.

-------------------------------------------

Releases

Release 1.9
- The major change in this version is the active power limits adjustment.
This will be useful to improve performance on some newer platform. But
this will will lead to increase in CPU and other temperatures. Hence this
is important to run dptfxtract version 1.4.1 tool to get performance
sensitive thermal limits (https://github.com/intel/dptfxtract/commits/v1.4.1).
If the default configuration picked up by thermald is not optimal, user
can select other less aggressive configuration. Refer to the README here
https://github.com/intel/dptfxtract/blob/master/README.txt

This power limit adjustment depends on some kernel changes released with
kernel version v5.3-rc1. For older kernel release run thermald with
--workaround-enabled
But this will depend on /dev/mem access, which means that platforms with
secure boot must update to newer kernels.

- TCC offset limits
As reported in some forums that some platforms have issue with high TCC
offset settings. Under some special condition this offset is adjusted.
But currently needs msr module loaded to get MSR access
from user space. I have submitted a patch to have this exported via sysfs
for v5.4+ kernel.

- To disable all the above performance optimization, use --disable-active-power.
Since Linux Thermal Daemon implementation doesn't have capability to match
Intel® Dynamic Platform and Thermal Framework (DPTF) implementation on other
Operating systems, this option is very important if the user is experiencing
thermal issues. If there is some OEM/manufactures have issue with this
implementation, please get back to me for blacklist of platforms.

- Added support for Ice Lake platform

- ThermalMonitor
Cleaned up the plots, so that only active sensors and trips gets plotted.

Release 1.8
- Support of KBL-G with discrete GPU
- Fast removal of any cooling action which was applied once
temperature is normal
- Android support
- Add Hot trip point, which when reached just calls "suspend"
- Adding new tag "DependsOn" which enable/disable trip based on some other trip
- Polling interval can be configured via thermal xml config
- Per trip PID control
- Simplify RAPL cooling device

Release 1.7.2
- Workwround for platform with invalid thermal table
- Error printing for RAPL constraint sysfs read on failure
- thermal-conf.xml.auto  can be read from /etc/thermald, which allows user to modify
generated thermal-conf.xml from /var/run/thermald and copy to /etc/thermald

Release 1.7.1
- Removed dptfxtract binary as there is an issue
in packaging this with GPL source for distributions

Release 1.7
- Add GeminiLake
- Add dptfxtract tool, which converts DPTF tables to thermald tables using best effort
- Changes to accommodate dptfxtract tool conversions
- Better facility to configure fan controls
- PID control optimization
- Fix powerlimit write errors because of bad FW settings of power limits
- More restrictive compile options and warnings as errors
- Improve logging
- Android build fixes

Release 1.6
- Add Kabylake and missing Broadwell CPU model
- Removed deprecated modules
- Added passive trip between critical and max, to allow fan to take control first
- Fixed clash when multiple zones and trips controlling same cdev

1.5.4
- Use Processor thermal device in lieu of CPU zone when present
- Haswell/Skylake PCH sensor
- Fix regression in LCD/Backlight path

Release 1.5.3
- PCH sensor support

Release 1.5.2
- Security bug for bios lock fix

Release 1.5.1
- Regression fix for the default config file location

Release 1.5
- Default warning level increase so that doesn't print much in logs
- Add new feature to set specific target state on reaching a threshold,
this allows multiple thresholds (trips)
- Android update for build
- Additional backlight devices
- New option to specify config file via command line
- Prevent adding cooling device in /etc via dbus
- Whitelist of processor models, to avoid startup on server platforms

Release 1.4.3
- One new dbus message to get temp
- Fixes to prevent warnings

Release 1.4
- Extension of DBUS I/F for developing Monitoring and Control GUI
- Added exampled to thermal-conf man page
- Support INT340X class of thermal control introduced in kernel 4.0
- Reinit without restart thermald to load new parameters like new control temperature
- Fix indexes when Linux thermal sysfs doesn't have contiguous zone numbering
- Support for new Intel SoC platforms
- Introduce back-light control as the Linux back light cooling device is removed
- Restore modified passive trip points in thermal zones on exit
- Virtual Sensor definition
- Fix loop when uevents floods the system
- Error message removal for rapl sysfs traversal
- Coverity error

Release 1.3
- Auto creation of configuration based on ACPI thermal relationship table
- Default CPU bound load check for unbinded thermal sensors

Release 1.2
- Several fixes for Klocworks and Coverity scans (0 issues remaining)
- Baytrail RAPL support as this doesn't have max power limit value

Release 1.1
- Use powercap Intel RAPL driver
- Use skin temperature sensor by default if available
- Specify thermal relationship
- Clean up for MSR related controls as up stream kernel driver are capable now
- Override capability of thermal sysfs for a specific sensor or zone
- Friendly to new thermal sysfs

Release 1.04
- Android and chrome os integration
- Minor fixes for buggy max temp

Release 1.03
- Allow negative step increments while configuring via XML
- Use powercap RAPL driver I/F
- Additional cpuids in the list
- Add man page with details of usage
- Added P state turbo on/off

Release 1.02
- Allow user to change the max temperarure via dbus message
- Allow user to change the cooling method order via an XML configuration
- Upstart fixes
- Valgrind and zero warnings on build

Release 1.01
- Implement RAPL using MSRs.
- User can configure cooling device order via XML config file
- Fix sensor path configuration for thermal-conf.xml, so that user cn specify custom sensor paths
- Use CPU max scaling frequency to control CPU Frequencies
- RPM generation scripts
- Build and formatting fixes from Alexander Bersenev


Release 1.0
- Tested on multiple platforms
- Using PID

version 0.9
- Replaced netlink with uevents
- Fix issue with pre-configured thermal data to control daemon
- Use pthreads

version 0.8
- Fix RAPL PATH, which is submitted upstream
- Handle case when there is no MSR access from user mode
- Allow non Intel CPUs

version 0.7
- Conditional per cpu control
- Family id check
- If no max use offset from critical temperature
- Switch to hwmon if there is no coretemp
- Error handling if MSR support is not enabled in kernel
- Code clean up and comments

Version 0.6
- Use Intel P state driver to control P states
- Use RAPL cooling device
- Fix valgrind reported errors and cleanup
- Add document

Version 0.5
- License update to GPL v2 or later
- Change dbus session bus to system
- Load thermal-conf.xml data if exact UUID  match

Version 0.4
- Added power clamp driver interface
- Added per cpu controls by trying to calibrate in the background to learn sensor cpu relationship
- Optimized p states and turbo states and cleaned up
- systemd and service start stop interface

Version 0.3
- Added P states t states turbo states as the cooling methods
- No longer depend on any thermal sysfs, zone cooling device by default
- Uses DTS core temperature and p/turbo/t states to cool system
- By default only will use DTS core temperature and p/turbo/t states only
- All the previous controls based on the zones/cdevs and XML configuration is only done, when activated via command line
- The set points are calculated and stored in a config file when it hits thermal threshold and adjusted based
on slope and angular increments to dynamically adjust set point


Version 0.2
- Define XML interface to set configuration data. Refere to thermal-conf.xml. This allows to override buggy Bios thermal comfiguration and also allows to extend the capability.
- Use platform DMI UUID to index into configuration data. If there is no UUID match, falls back to thermal sysfs
- Terminate interface
- Takes over control from kernel thermal processing
- Clean up of classes.


Version 0.1
- Dbus interface to set preferred policy: "performance", "quiet/power", "disabled"
- Defines a C++ classes for zones, cooling devices, trip points, thermal engine
- Methods can be overridden in a custom class to modify default behaviour
- Read thermal zone and cooling devices, trip points etc,
- Read temprature via netlink notification or via polling configurable via command line
- Once a trip point is crossed, activate the associate cooling devices. Start with min tstate to max tstate for each cooling device.
- Based on active or passive settings it decides the cooling devices

