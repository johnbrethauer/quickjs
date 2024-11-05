project('quickjs', 'c')

cc = meson.get_compiler('c')
m_dep = cc.find_library('m', required:false)

add_project_arguments('-DCONFIG_VERSION="2024-02-14"', language : 'c')

if get_option('bignum')
  add_project_arguments('-DCONFIG_BIGNUM', language : 'c')
endif

lib_sources = ['libbf.c', 'libregexp.c', 'quickjs.c', 'libunicode.c', 'cutils.c']

libquickjs = library('quickjs',
  lib_sources,
  dependencies: m_dep
)

quickjs_dep = declare_dependency(link_with: libquickjs, include_directories: include_directories('.'), dependencies: m_dep)

qjsc = executable('qjsc',
  'qjsc.c', 'quickjs-libc.c',
  dependencies: quickjs_dep
)

qjscalc_c = custom_target(
  'qjscalc_c',
  output: 'qjscalc.c',
  input: 'qjscalc.js',
  command: [qjsc, '-fbignum', '-c', '-o', '@OUTPUT@', '@INPUT@'],
)

qjsrepl_c = custom_target(
  'qjsrepl_c',
  output: 'repl.c',
  input: 'repl.js',
  command: [qjsc, '-fbignum', '-c', '-o', '@OUTPUT@', '-m', '@INPUT@'],
)

qjs = executable('qjs',
  'qjs.c', 'quickjs-libc.c', qjscalc_c, qjsrepl_c,
  dependencies: quickjs_dep
)