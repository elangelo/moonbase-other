(

  AUDIO_LIST="oss"

  in_depends $MODULE alsa-lib && AUDIO_LIST+=" alsa"
  in_depends $MODULE esound && AUDIO_LIST+=" esd"
  in_depends $MODULE pulseaudio && AUDIO_LIST+=" pa"
  in_depends $MODULE SDL && AUDIO_LIST+=" sdl"

  add_priv_group kvm &&

  ./configure --prefix=/usr \
              --sysconfdir=/etc \
              --audio-drv-list="$AUDIO_LIST" \
              $OPTS &&

  default_make &&
  if module_installed systemd; then
    install -D -m0644 $SCRIPT_DIRECTORY/65-kvm.rules /usr/lib/udev/rules.d/65-kvm.rules
  else
    install -D -m0644 $SCRIPT_DIRECTORY/65-kvm.rules /lib/udev/rules.d/65-kvm.rules
  fi &&
  ln -s qemu-system-x86_64 /usr/bin/qemu-kvm &&
  ln -s qemu-system-x86_64 /usr/bin/qemu &&
  ln -s qemu-system-x86_64 /usr/bin/kvm &&
  mv /usr/share/man/man1/qemu.1 /usr/share/man/man1/qemu-kvm.1

) > $C_FIFO 2>&1
