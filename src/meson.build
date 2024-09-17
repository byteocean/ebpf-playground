# Build BPF code
bpf_prog = custom_target('playground_bpf',
	input : 'playground.bpf.c',
	output : 'playground.bpf.built.o',
	command : [clang, '-g', '-O2', '-target', 'bpf',
				'-D__TARGET_ARCH_' + arch, '-I' + meson.current_source_dir(),
				'-I' + libbpf_src, clang_includes, '-c', '@INPUT@', '-o', '@OUTPUT@'],
	depends : [libbpf, bpftool],
	build_by_default : true
)

# Strip debug info from BPF object
stripped_bpf = custom_target('stripped_bpf',
	input : bpf_prog,
	output : 'playground.bpf.o',
	command : [llvm_strip, '-g', '@INPUT@', '-o', '@OUTPUT@'],
	build_by_default : true
)

# Generate BPF skeleton
bpf_skel = custom_target('bpf_skel',
	input : stripped_bpf,
	output : 'playground.skel.h',
	command : [bpftool, 'gen', 'skeleton', '@INPUT@'],
	capture : true,
	build_by_default : true
)

# Main application
executable('playground',
	['playground.c', bpf_skel],
	include_directories : inc,
	dependencies : [libbpf_dep, libelf, libz, thread_dep],
	link_with : libbpf,
	install : true
)
