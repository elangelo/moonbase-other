(

  patch_it $SOURCE_CACHE/$SOURCE2 1 &&

  default_cmake_build &&

# It doesn't install these binaries
  install bin/opencv_test* /usr/bin

) > $C_FIFO 2>&1
