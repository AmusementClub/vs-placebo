project('vs-placebo', ['c', 'cpp'],
  default_options: ['buildtype=release', 'b_ndebug=if-release', 'c_std=c11', 'cpp_std=c++11'],
  meson_version: '>=0.51.0',
  version: '1.4.3'
)

win32 = host_machine.system() == 'windows' or host_machine.system() == 'cgywin'

if win32
  add_project_link_arguments('-static', language: ['c', 'cpp'])
endif

libplacebo_default_options = [
  'default_library=static',
  'vulkan=enabled',
  'opengl=disabled',
  'd3d11=disabled',
  'glslang=enabled',
  'shaderc=enabled',
  'lcms=enabled',
  'demos=false',
]
if win32
  libplacebo_default_options += ['shaderc=enabled']
endif

placebo = subproject('libplacebo', default_options: libplacebo_default_options).get_variable('libplacebo')

cargo = find_program('cargo', required: false)
cargo_c = find_program('cargo-cinstall', required: false)
use_dovi = cargo.found() and cargo_c.found()

if use_dovi
  dovi_src = join_paths(meson.current_source_dir(), 'dovi_tool', 'dolby_vision', 'Cargo.toml')
  dovi_dst = join_paths(meson.current_build_dir(), 'libdovi.d')
  run_command('cargo', 'cinstall', '--release', '--manifest-path=@0@'.format(dovi_src), '--prefix=@0@'.format(dovi_dst))
endif

dovi = dependency('dovi', required: false)

if win32 and not dovi.found()
  cc = meson.get_compiler('c')
  dovi = cc.find_library('dovi', required : false)
endif

use_dovi = dovi.found()

config_vsplacebo = configuration_data()
config_vsplacebo.set('HAVE_DOVI', use_dovi)

configure_file(
  output: 'config_vsplacebo.h',
  configuration: config_vsplacebo,
)

p2p = static_library('p2p', ['libp2p/p2p_api.cpp', 'libp2p/v210.cpp'], pic: true)

sources = []

subdir('src')

if win32
  subdir('vulkan')
  vulkan = declare_dependency(link_args: 'vulkan/libvulkan-1.a')
else
  vulkan = declare_dependency()
endif

shared_module('vs_placebo', sources,
  dependencies: [placebo, vulkan, dovi],
  link_with: [p2p],
  name_prefix: 'lib',
  install: true
)
