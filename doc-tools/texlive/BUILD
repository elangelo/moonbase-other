(

  # Patch from http://scmweb.sourcemage.org/?p=smgl/grimoire.git;a=tree;f=latex/texlive
  # Use Linux-sensible directories
  patch_it $SOURCE_CACHE/$SOURCE2 0  &&

  # Fonts dir
  sedit 's:^\(OSFONTDIR =\).*:\1 /usr/share/fonts:' texk/kpathsea/texmf.cnf  &&

  # Use /usr/share/texmf and /usr/share/texmf-dist
  find -name Makefile\* -print0 | xargs -0 sed -i -e "s:\${prefix}/texmf:\${datadir}/texmf:"  &&
  find -name configure -print0 | xargs -0 sed -i -e "s:\${prefix}/texmf:\${datadir}/texmf:"  &&
  find -name \*.info -print0 | xargs -0 sed -i -e "s:/usr/local:/usr:"  &&
  find -name \*.texi -print0 | xargs -0 sed -i -e "s:/usr/local:/usr:"  &&
  find -name \*.synctex -print0 | xargs -0 sed -i -e "s:/usr/local:/usr:"  &&

  # Direct symlinks from /usr/bin/ into /usr/*share*/texmf... rather than /usr/texmf...
  sedit "s:\$(REL):/usr/share:" texk/texlive/linked_scripts/Makefile*  &&
  # Some of the symlinks are still wrong, e.g.:
  # xmltools -> ../share/texmf-dist/scripts/context/stubs/unix/xmltools
  # presumably should be:
  # xmltools -> ../share/texmf-dist/scripts/context/ruby/xmltools.rb
  # Show them by running:  ls -l /usr/bin | grep stubs
  
  # Prevent the ridiculous behaviour of deleting, then trying to replace, /usr/bin/man
  sedit "s:rm -f man:true:" texk/texlive/linked_scripts/Makefile*  &&
  sedit "s:ln -s /usr/share/texmf/doc/man man:true:" texk/texlive/linked_scripts/Makefile*  &&

  # Hide warnings about being unable to find its own config files
  export KPATHSEA_WARNING=0  &&

  # Some options are from http://www.gentoo-portage.com/app-text/texlive-core
  # and http://repos.archlinux.org/wsvn/packages/texlive-bin/trunk/
  # xindy should be in its own module, because its compilation needs latex to be *already* installed.
  # luatex is provided by the luatex module.
  OPTS+=" --datadir=/usr/share --disable-multiplatform --disable-native-texlive-build --with-system-zlib --with-system-freetype2 --with-system-libpng --with-gs=gs --disable-xindy --enable-etex --disable-luatex --enable-xetex --enable-ipc --enable-shared --enable-static=no"  &&

  default_build  &&

  # Idea from http://repos.archlinux.org/wsvn/packages/texlive-bin/trunk/
  install -m644 $SOURCE_CACHE/$SOURCE3 /etc/fonts/conf.d/09-texlive-fonts.conf

) > $C_FIFO 2>&1