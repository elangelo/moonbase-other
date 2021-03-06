(

  # Workaround Segmentation fault at install part on precompile_cache.js
  sedit '/^GENERATE_CACHE = 1$/d' browser/installer/Makefile.in &&

  sedit 's/@PRE_RELEASE_SUFFIX@//g' browser/base/content/browser.xul &&

  if module_is_expired $MODULE && [ "$VERSION" != "`installed_version $MODULE`" ]; then
        set_module_config OLD_VER "`installed_version $MODULE`"
  fi  &&

  # Adds support for libvpx 1.0. Later firefox will have this fix so remove this then.
  sedit 's/VPX_CODEC_USE_INPUT_PARTITION/VPX_CODEC_USE_INPUT_FRAGMENTS/' configure  &&
  sedit 's/v0\.9\.7/v1.0.0/' configure  &&

  export MOZ_CO_PROJECT=browser  &&
  export MOZILLA_HOME=/usr/lib/$MODULE-$VERSION &&
  export MOZILLA_OFFICIAL=1  &&
  export BUILD_OFFICIAL=1  &&
  export MOZ_MAKE_FILES=$MAKES  &&
  export MOZ_OPTIMIZE_FLAGS="$CFLAGS" &&
  export MOZ_DEBUG_FLAGS="$CFLAGS" &&

  cp $SCRIPT_DIRECTORY/mozconfig .  &&
  export MOZ_OBJDIR="${SOURCE_DIRECTORY}/build-mozilla"  &&
  mkdir -p ${MOZ_OBJDIR}  &&

  #Add DEPENDS options

  if in_depends $MODULE gnome-vfs ; then
    echo "ac_add_options --enable-gnomevfs" >> mozconfig
  else
    echo "ac_add_options --disable-gnomevfs" >> mozconfig
  fi  &&

  if in_depends $MODULE libevent ; then
    echo "ac_add_options --with-system-libevent" >> mozconfig
  else
    echo "ac_add_options --without-system-libevent" >> mozconfig
  fi  &&

  if in_depends $MODULE dbus-glib ; then
    echo "ac_add_options --enable-dbus" >> mozconfig
  else
    echo "ac_add_options --disable-dbus" >> mozconfig
  fi  &&

  if in_depends $MODULE libffi ; then
    echo "ac_add_options --enable-system-ffi" >> mozconfig
  else
    echo "ac_add_options --disable-system-ffi" >> mozconfig
  fi  &&

  if in_depends $MODULE sun-jdk ; then
    echo "ac_add_options --with-java-bin-path=/usr/java/default/bin" >> mozconfig
  fi  &&

  #Add CONFIGURE options

  if [ "$SAFE" == "y" ] ; then
    echo "ac_add_options --enable-safe-browsing" >> mozconfig
  fi  &&

  setterm -bfreq -blength &&

  if [ -n "${MAKES}" ]; then
    echo "mk_add_options MOZ_MAKE_FLAGS='-j${MAKES}'" >> mozconfig
  fi &&

  #Finally, the build!
  if [ "$PGO" == "y" ] ; then
    #CCache breaks the pgo build
    export CCACHE_DISABLE=1  &&
    echo "ac_add_options --enable-profile-guided-optimization" >> mozconfig  &&
    echo "mk_add_options PROFILE_GEN_SCRIPT='python $MOZ_OBJDIR/_profile/pgo/profileserver.py'" >> mozconfig  &&
    make -f client.mk build &&
    make -f client.mk profiledbuild
  else
    make -f client.mk build
  fi  &&

  cd ${MOZ_OBJDIR}  &&
  prepare_install   &&
  make install      || exit 1

  # Now the .desktop file and icons
  install -Dm 644 {"$SCRIPT_DIRECTORY",/usr/share/applications}/firefox.desktop  &&

  for s in 16 22 24 32 48 256 ; do
    install -Dm 644 $SOURCE_DIRECTORY/browser/branding/official/default${s}.png /usr/share/icons/hicolor/${s}x${s}/apps/firefox.png
  done  &&
  if [ -x /usr/bin/gtk-update-icon-cache -a -f /usr/share/icons/hicolor/index.theme ]; then
    gtk-update-icon-cache -f /usr/share/icons/hicolor
  fi

) > $C_FIFO 2>&1
