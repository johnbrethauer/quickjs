project('quickjs', 'c')

cc = meson.get_compiler('c')

deps = []
deps += cc.find_library('m', required:false)

add_project_arguments('-DCONFIG_VERSION="2024-02-14"', language : 'c')

if get_option('bignum')
  add_project_arguments('-DCONFIG_BIGNUM', language : 'c')
endif

if get_option('trace')
  add_project_arguments('-DTRACY_ENABLE', language:'c')
  deps += dependency('tracy', static:true)
endif

lib_sources = ['libbf.c', 'libregexp.c', 'quickjs.c', 'libunicode.c', 'cutils.c']

libquickjs = library('quickjs',
  lib_sources,
  dependencies: deps
)

quickjs_dep = declare_dependency(link_with: libquickjs, include_directories: include_directories('.'), dependencies: deps)

threads = dependency('threads')

qjsc = executable('qjsc',
  'qjsc.c', 'quickjs-libc.c',
  dependencies: [quickjs_dep,threads],
  build_by_default:false
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
  dependencies: [quickjs_dep, threads],
  build_by_default:false
)

test_root = join_paths(meson.project_source_root(), 'tests')

base_tests = {
  'closure': ['test_closure.js'],
  'language': ['test_language.js'],
  'loop': ['test_loop.js'],
  'op_overloading': ['--bignum', 'test_op_overloading.js'],
  'bignum': ['--bignum', 'test_bignum.js'],
  'qjscalc': ['--qjscalc', 'test_qjscalc.js']
}

test_suite = base_tests

is_windows = host_machine.system() == 'windows'
if not is_windows
  test_suite += {
    'builtin': ['test_builtin.js'],
    'std': ['test_std.js'],
    'worker': ['test_worker.js']
  }
endif

foreach test_name, test_args : test_suite
  test(test_name, qjs, args: test_args, workdir: test_root)
endforeach

benchmark('microbench', qjs, args: ['microbench.js'], workdir: test_root, timeout: 0)