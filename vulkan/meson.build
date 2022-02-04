src = join_paths(meson.current_source_dir(), 'vulkan-1.def')
dst = join_paths(meson.current_build_dir(), 'libvulkan-1.a')
r = run_command('dlltool', '-d', src, '-l', dst)