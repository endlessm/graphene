graphene_bench_sources = [
  'graphene-bench-utils.h',
  'graphene-bench-utils.c'
]

graphene_bench = static_library('graphene-bench', graphene_bench_sources,
                                c_args: [
                                  '-DG_LOG_DOMAIN="Graphene-Bench"'
                                ],
                                include_directories: [ graphene_inc ],
                                install: false,
                                dependencies: [ mathlib, graphene_dep ])
graphene_bench_dep = declare_dependency(link_with: [ graphene_bench ])

# XXX: Would be nicer if we could do: have_FOO = 'FOO' in ARRAY
have_sse = false
have_gcc = false
have_neon = false
have_scalar = true
foreach s: graphene_simd
  if s == 'sse2'
    have_sse = true
  endif
  if s == 'gcc'
    have_gcc = true
  endif
  if s == 'neon'
    have_neon = true
  endif
endforeach

all_simds = [
  [ 'SSE', 'sse', have_sse ],
  [ 'GCC', 'gcc', have_gcc ],
  [ 'ARM_NEON', 'neon', have_neon ],
  [ 'SCALAR', 'scalar', have_scalar ]
]

bench_units = [
  'matrix'
]

foreach simd: all_simds
  run_test = simd[2]
  if run_test
    foreach unit: bench_units
      exe = executable('@0@-@1@'.format(unit, simd[1]), unit + '.c',
                       c_args: [
                         '-DGRAPHENE_COMPILATION',
                         '-DGRAPHENE_SIMD_BENCHMARK',
                         '-DGRAPHENE_HAS_' + simd[0],
                       ],
                       install: false,
                       dependencies: [ graphene_bench_dep, graphene_dep ])
      benchmark('@0@-@1@'.format(unit, simd[1]), exe)
    endforeach
  endif
endforeach
