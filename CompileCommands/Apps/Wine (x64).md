## Wine

Install all dependencies as following https://wiki.winehq.org/Building_Wine

Add the following patchfile to fix https://bugs.winehq.org/show_bug.cgi?id=55070
`partial-revert-f1d4dd7cc83d971c6e69c3f2bdffe85dbcd81c0a-v2.patch`
```
--- a/programs/winemenubuilder/winemenubuilder.c
+++ b/programs/winemenubuilder/winemenubuilder.c
@@ -1264,7 +1264,8 @@ static BOOL write_desktop_entry(const WCHAR *link, const WCHAR *location, const
     char *workdir_unix;
     int needs_chmod = FALSE;
     const WCHAR *name;
-    const WCHAR *prefix = _wgetenv( L"WINECONFIGDIR" );
+    const char *prefix = getenv("WINEPREFIX");
+    const char *home = getenv("HOME");
 
     WINE_TRACE("(%s,%s,%s,%s,%s,%s,%s,%s,%s)\n", wine_dbgstr_w(link), wine_dbgstr_w(location),
                wine_dbgstr_w(linkname), wine_dbgstr_w(path), wine_dbgstr_w(args),
@@ -1286,11 +1287,9 @@ static BOOL write_desktop_entry(const WCHAR *link, const WCHAR *location, const
     fprintf(file, "Name=%s\n", wchars_to_utf8_chars(name));
     fprintf(file, "Exec=" );
     if (prefix)
-    {
-        char *path = wine_get_unix_file_name( prefix );
-        fprintf(file, "env WINEPREFIX=\"%s\" ", path);
-        heap_free( path );
-    }
+        fprintf(file, "env WINEPREFIX=\"%s\" ", prefix);
+    else if (home)
+        fprintf(file, "env WINEPREFIX=\"%s/.wine\" ", home);
     fprintf(file, "wine %s", escape(path));
     if (args) fprintf(file, " %s", escape(args) );
     fputc( '\n', file );
@@ -2034,7 +2033,8 @@ static BOOL write_freedesktop_association_entry(const WCHAR *desktopPath, const
 {
     BOOL ret = FALSE;
     FILE *desktop;
-    const WCHAR *prefix = _wgetenv( L"WINECONFIGDIR" );
+    const char *prefix = getenv("WINEPREFIX");
+    const char *home = getenv("HOME");
 
     WINE_TRACE("friendlyAppName=%s, MIME type %s, progID=%s, icon=%s to file %s\n",
                wine_dbgstr_w(friendlyAppName), wine_dbgstr_w(mimeType),
@@ -2048,11 +2048,9 @@ static BOOL write_freedesktop_association_entry(const WCHAR *desktopPath, const
         fprintf(desktop, "Name=%s\n", wchars_to_utf8_chars(friendlyAppName));
         fprintf(desktop, "MimeType=%s;\n", wchars_to_utf8_chars(mimeType));
         if (prefix)
-        {
-            char *path = wine_get_unix_file_name( prefix );
-            fprintf(desktop, "Exec=env WINEPREFIX=\"%s\" wine start ", path);
-            heap_free( path );
-        }
+            fprintf(desktop, "Exec=env WINEPREFIX=\"%s\" wine start ", prefix);
+        else if (home)
+            fprintf(desktop, "Exec=env WINEPREFIX=\"%s/.wine\" wine start ", home);
         else
             fprintf(desktop, "Exec=wine start ");
         if (progId) /* file association */
```

add `check-address-space-limit-on-all-archs.patch`
```
diff --git a/dlls/ntdll/unix/virtual.c b/dlls/ntdll/unix/virtual.c
index 0d88315..87b27fb 100644
--- a/dlls/ntdll/unix/virtual.c
+++ b/dlls/ntdll/unix/virtual.c
@@ -2436,8 +2436,6 @@ static NTSTATUS map_pe_header( void *ptr, size_t size, int fd, BOOL *removable )
     return STATUS_SUCCESS;  /* page protections will be updated later */
 }
 
-#ifdef __aarch64__
-
 /***********************************************************************
  *           get_host_addr_space_limit
  */
@@ -2464,6 +2462,7 @@ static void *get_host_addr_space_limit(void)
     return (void *)((addr << 1) - (granularity_mask + 1));
 }
 
+#ifdef __aarch64__
 
 /***********************************************************************
  *           alloc_arm64ec_map
@@ -3296,12 +3295,8 @@ void virtual_init(void)
     pthread_mutex_init( &virtual_mutex, &attr );
     pthread_mutexattr_destroy( &attr );
 
-#ifdef __aarch64__
     host_addr_space_limit = get_host_addr_space_limit();
     TRACE( "host addr space limit: %p\n", host_addr_space_limit );
-#else
-    host_addr_space_limit = address_space_limit;
-#endif
 
     if (preload_info && *preload_info)
         for (i = 0; (*preload_info)[i].size; i++)
```

`Wine (x64)`
should be built on a bionic amd64 chroot
```bash
# download and extract sources
version=9.13
mkdir -p ~/wine
cd ~/wine
wget https://dl.winehq.org/wine/source/9.x/wine-${version}.tar.xz
tar -xvf wine-${version}.tar.xz
# apply patch
cd wine-${version}
git apply ../partial-revert-f1d4dd7cc83d971c6e69c3f2bdffe85dbcd81c0a-v2.patch
git apply ../check-address-space-limit-on-all-archs.patch
cd ..
# build
mkdir wine-${version}-build
cd wine-${version}-build
../wine-${version}/configure --enable-archs=i386,x86_64 --prefix=/opt/wine-${version}
make -j12
# install
sudo make install
# package
cd /opt
sudo tar -czvf ~/wine/wine-${version}.tar.gz wine-${version}
# upload to github releases
gh release --repo Pi-Apps-Coders/files upload large-files ~/wine/wine-${version}.tar.gz
```
