# ARM64 Cross compiler for Mac OS (aarch64-unknown-linux-gnu)

This is tool-chain to build ARM64 Linux kernel on Mac OS

## How to use

This instruction is tested at 20/12/24 with stable kernel
(8653b778e454a7708847aeafe689bce07aeeb94e)

### file2alias.c

First, you should patch file2alias.c as below:

``` patch
@@ -38,6 +39,10 @@ typedef struct {
		__u8 b[16];
 } guid_t;

+#ifdef __APPLE__
+#define uuid_t compat_uuid_t
+#endif
+
 /* backwards compatibility, don't use in new code */
 typedef struct {
		__u8 b[16];
@@ -84,15 +89,15 @@ struct devtable {

 #define ADD(str, sep, cond, field)                              \
 do {                                                            \
-        strcat(str, sep);                                       \
-        if (cond)                                               \
-                sprintf(str + strlen(str),                      \
-                        sizeof(field) == 1 ? "%02X" :           \
-                        sizeof(field) == 2 ? "%04X" :           \
-                        sizeof(field) == 4 ? "%08X" : "",       \
-                        field);                                 \
-        else                                                    \
-                sprintf(str + strlen(str), "*");                \
+       strcat(str, sep);                                       \
+       if (cond)                                               \
+               sprintf(str + strlen(str),                      \
+                       sizeof(field) == 1 ? "%02X" :           \
+                       sizeof(field) == 2 ? "%04X" :           \
+                       sizeof(field) == 4 ? "%08X" : "",       \
+                       field);                                 \
+       else                                                    \
+               sprintf(str + strlen(str), "*");                \
 } while(0)
```

### Build Kernel

Build kernel with following arguments (include path needs to be
set). Before kernel compilation, openssl@1.1 should be installed.

``` shell
CROSS_COMPILE=aarch64-unknown-linux-gnu- ARCH=arm64 HOSTCFLAGS="-I/usr/local/opt/openssl@1.1/include -L/usr/local/opt/openssl/lib -I/usr/local/include" make
```
