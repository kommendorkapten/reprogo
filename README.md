# Go reproducibility/diffoscope

``` ls -l
go-0* # Locally built and signed
go-1* # Upstream binary, signed by google
go-stripped-0* # Local built binary, signature stripped
go-stripped-1* # Google built binary, signature stripped
```

SHA2-256 digests:
```
fc94bbe0abf124964ec7b55163537bf655ff509a2e5abb1c374f1285ff085e4d go-0
0935bcda59d2635a89103dc1e147a18aede115a2ce1e0f11f0afc2aa0c3ac5d1 go-1
dcd4b6f067e15e3fb872900254628d460deb40ae2c5b4c502b9be6e8a083707e go-stripped-0
dcd4b6f067e15e3fb872900254628d460deb40ae2c5b4c502b9be6e8a083707e go-stripped-1
```

```
$  diffoscope go-0 go-stripped-0
--- go-0
+++ go-stripped-0
├── arm64
│ ├── otool -arch arm64 -h {}
│ │ @@ -1,3 +1,3 @@
│ │  Mach header
│ │        magic  cputype cpusubtype  caps    filetype ncmds sizeofcmds      flags
│ │ - 0xfeedfacf 16777228          0  0x00           2    16       2040 0x00200004
│ │ + 0xfeedfacf 16777228          0  0x00           2    15       2024 0x00200004
```

View signatures

Unsigned file
```
$ codesign -dv --verbose=4 go-stripped-0
go-stripped-0: code object is not signed at all
```

Self Signed file (i.e built locally)
```
$ codesign -dv --verbose=4 go-0
Executable=/Users/kommendorkapten/TMP/go/go-0
Identifier=a.out
Format=Mach-O thin (arm64)
CodeDirectory v=20400 size=93630 flags=0x20002(adhoc,linker-signed) hashes=2923+0 location=embedded
VersionPlatform=1
VersionMin=720896
VersionSDK=720896
Hash type=sha256 size=32
CandidateCDHash sha256=7654f0a68e469ab3bbde48e0dcf451f8c1fcb8ea
CandidateCDHashFull sha256=7654f0a68e469ab3bbde48e0dcf451f8c1fcb8eae3429e9bb8b943f5683d56c1
Hash choices=sha256
CMSDigest=7654f0a68e469ab3bbde48e0dcf451f8c1fcb8eae3429e9bb8b943f5683d56c1
CMSDigestType=2
Executable Segment base=0
Executable Segment limit=6324224
Executable Segment flags=0x1
Page size=4096
Launch Constraints:
	None
CDHash=7654f0a68e469ab3bbde48e0dcf451f8c1fcb8ea
Signature=adhoc
Info.plist=not bound
TeamIdentifier=not set
Sealed Resources=none
Internal requirements=none
```

Signed by Google
```
$ codesign -dv --verbose=4 go-1
Executable=/Users/kommendorkapten/TMP/go/go-1
Identifier=org.golang.go
Format=Mach-O thin (arm64)
CodeDirectory v=20500 size=93721 flags=0x10000(runtime) hashes=2923+2 location=embedded
VersionPlatform=1
VersionMin=720896
VersionSDK=720896
Hash type=sha256 size=32
CandidateCDHash sha256=831ff04336bd1044b0abb8872fcfa0e74bc23abd
CandidateCDHashFull sha256=831ff04336bd1044b0abb8872fcfa0e74bc23abd31cc892ba05b35f3f5af85b2
Hash choices=sha256
CMSDigest=831ff04336bd1044b0abb8872fcfa0e74bc23abd31cc892ba05b35f3f5af85b2
CMSDigestType=2
Executable Segment base=0
Executable Segment limit=6324224
Executable Segment flags=0x1
Page size=4096
Launch Constraints:
	None
CDHash=831ff04336bd1044b0abb8872fcfa0e74bc23abd
Signature size=8990
Authority=Developer ID Application: Google LLC (EQHXZ8M8AV)
Authority=Developer ID Certification Authority
Authority=Apple Root CA
Timestamp=9 Oct 2023 at 19:20:15
Info.plist=not bound
TeamIdentifier=EQHXZ8M8AV
Runtime Version=11.0.0
Sealed Resources=none
Internal requirements count=1 size=176
```

Dump commands

```
$ otool -l go-0
...
Load command 15
      cmd LC_CODE_SIGNATURE
  cmdsize 16
  dataoff 11970256
 datasize 93650
```

```
$ otool -l go-stripped-0
...
Load command 14
          cmd LC_LOAD_DYLIB
      cmdsize 96
         name /System/Library/Frameworks/Security.framework/Versions/A/Security (offset 24)
   time stamp 0 Thu Jan  1 01:00:00 1970
      current version 0.0.0
compatibility version 0.0.0
```
