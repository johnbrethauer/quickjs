project('quickjs', 'c')

cc = meson.get_compiler('c')
m_dep = cc.find_library('m', required:false)

add_project_arguments('-DCONFIG_VERSION="2024-02-14"', language : 'c')
add_project_arguments('-DCONFIG_BIGNUM', language : 'c')

lib_sources = ['libbf.c', 'libregexp.c', 'quickjs.c', 'libunicode.c', 'cutils.c','quickjs-libc.c']

libquickjs = library('quickjs',
  lib_sources,
  dependencies: m_dep
)

quickjs_dep = declare_dependency(link_with: libquickjs, include_directories: include_directories('.'), dependencies: m_dep)
