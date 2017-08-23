# Design & Architecture

## Dalvik VM
The DalvikVM is a register-based VM that interprets the Dalvik Executable (DEX) byte code format. 

## Understanding Security Boundaries and Enforcement
* Security boundaries, sometimes called trust boundaries, are specifi c places
within a system where the level of trust differs on either side. A great example
is the boundary between kernel-space and user-space. Code in kernel-space is
trusted to perform low-level operations on hardware and access all virtual and
physical memory. However, user-space code cannot access all memory due to
the boundary enforced by the central processing unit (CPU).
* The Android operating system utilizes two separate, but cooperating, permissions
models. At the low level, the Linux kernel enforces permissions using
users and groups. This permissions model is inherited from Linux and enforces
access to fi le system entries, as well as other Android specifi c resources. This is
commonly referred to as Android’s sandbox. The Android runtime, by way of
the DalvikVM and Android framework, enforces the second model. This model,
which is exposed to users when they install applications, defi nes app permissions
that limit the abilities of Android applications. Some permissions from the
second model actually map directly to specifi c users, groups, and capabilities
on the underlying operating system (OS).

## Android Sandbox
* Android's foundation of Linux brings with it a well-understood heritage of
Unix-like process isolation and the principle of least privilege. Specifi cally, the
concept that processes running as separate users cannot interfere with each
other, such as sending signals or accessing one another’s memory space. Ergo,
much of Android’s sandbox is predicated on a few key concepts: standard
Linux process isolation, unique user IDs (UIDs) for most processes, and tightly
restricted fi le system permissions.
Android shares Linux’s UID/group ID (GID) paradigm, but does not have the
traditional passwd and group fi les for its source of user and group credentials. 

* When applications execute, their UID, GID, and supplementary groups are
assigned to the newly created process. Running under a unique UID and GID
enables the operating system to enforce lower-level restrictions in the kernel,
and for the runtime to control inter-app interaction. This is the crux of the
Android sandbox. Applications can also share UIDs, by way of a special directive in the
application package. 

## Android Permissions
* The Android permissions model is multifaceted: There are API permissions, fi le
system permissions, and IPC permissions. Oftentimes, there is an intertwining
of each of these. As previously mentioned, some high-level permissions map
back to lower-level OS capabilities. This could include actions such as opening
sockets, Bluetooth devices, and certain fi le system paths.
To determine the app user’s rights and supplemental groups, Android processes
high-level permissions specifi ed in an app package’s AndroidManifest
.xml fi le (the manifest and permissions are covered in more detail in the “Major
Application Components” section). Applications’ permissions are extracted from
the application’s manifest at install time by the PackageManager and stored in
`/data/system/packages.xml`. 

* These entries are then used to grant the appropriate rights at the instantiation of the app’s process (such as setting supplemental
GIDs). The following snippet shows the Google Chrome package entry inside
`packages.xml`, including the unique userId for this app as well as the permissions
it requests:

```
<package name="com.android.chrome"
codePath="/data/app/com.android.chrome-1.apk"
nativeLibraryPath="/data/data/com.android.chrome/lib"
flags="0" ft="1422a161aa8" it="1422a163b1a"
ut="1422a163b1a" version="1599092" userId="10082"
installer="com.android.vending">
<sigs count="1">
<cert index="0" />
</sigs>
<perms>
<item name="com.android.launcher.permission.INSTALL_SHORTCUT" />
<item name="android.permission.NFC" />
...
<item name="android.permission.WRITE_EXTERNAL_STORAGE" />
<item name="android.permission.ACCESS_COARSE_LOCATION" />
...
<item name="android.permission.CAMERA" />
<item name="android.permission.INTERNET" />
...
</perms>
</package>
```

* The permission-to-group mappings are stored in `/etc/permissions/
platform.xml`. These are used to determine supplemental group IDs to set for
the application. The following snippet shows some of these mappings:

```
 <permission name="android.permission.INTERNET" >
 <group gid="inet" />
 </permission>
 <permission name="android.permission.CAMERA" >
 <group gid="camera" />
 </permission>
 <permission name="android.permission.READ_LOGS" >
 <group gid="log" />
 </permission>
 <permission name="android.permission.WRITE_EXTERNAL_STORAGE" >
 <group gid="sdcard_rw" />
 </permission>
 ```
