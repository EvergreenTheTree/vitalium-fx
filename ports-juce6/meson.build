###############################################################################
# build flags for plugins

build_flags_plugin = [
    '-Werror=deprecated-declarations',
]

build_flag_plugin_cpp = [
    '-std=gnu++14',
]

###############################################################################
# format-specific build flags

build_flags_plugin_lv2 = [
    '-DJucePlugin_Build_AU=0',
    '-DJucePlugin_Build_LV2=1',
    '-DJucePlugin_Build_RTAS=0',
    '-DJucePlugin_Build_VST=0',
    '-DJucePlugin_Build_VST3=0',
    '-DJucePlugin_Build_Standalone=0',
    '-DBINTYPE=@0@LV2'.format(bintype_prefix),
    '-Wno-parentheses',
    '-Wno-deprecated-declarations', # FIXME
]

build_flags_plugin_vst2 = [
    '-DJucePlugin_Build_AU=0',
    '-DJucePlugin_Build_LV2=0',
    '-DJucePlugin_Build_RTAS=0',
    '-DJucePlugin_Build_VST=1',
    '-DJucePlugin_Build_VST3=0',
    '-DJucePlugin_Build_Standalone=0',
    '-DBINTYPE=@0@VST2'.format(bintype_prefix),
    '-Wno-write-strings',
]

build_flags_plugin_vst3 = [
    '-DJucePlugin_Build_AU=0',
    '-DJucePlugin_Build_LV2=0',
    '-DJucePlugin_Build_RTAS=0',
    '-DJucePlugin_Build_VST=0',
    '-DJucePlugin_Build_VST3=1',
    '-DJucePlugin_Build_Standalone=0',
    '-DBINTYPE=@0@VST3'.format(bintype_prefix),
    '-Wno-non-virtual-dtor',
]

if os_windows
    build_flags_plugin_vst3 += [
        '-D_NATIVE_WCHAR_T_DEFINED',
        '-D__wchar_t=wchar_t',
    ]
endif

###############################################################################
# format-specific link flags

link_flags_plugin_common = [
]

if os_darwin
    link_flags_plugin_common += [
        '-framework', 'Accelerate',
        '-framework', 'AudioToolbox',
        '-framework', 'AudioUnit',
        '-framework', 'Carbon',
        '-framework', 'Cocoa',
        '-framework', 'CoreAudio',
        '-framework', 'CoreAudioKit',
        '-framework', 'CoreMIDI',
        '-framework', 'IOKit',
        '-framework', 'QuartzCore',
        '-framework', 'WebKit',
    ]
    link_flags_plugin_lv2 = [
        '-Wl,-exported_symbol,_lv2_generate_ttl',
        '-Wl,-exported_symbol,_lv2_descriptor',
        '-Wl,-exported_symbol,_lv2ui_descriptor',
    ]
    link_flags_plugin_vst2 = [
        '-Wl,-exported_symbol,_VSTPluginMain',
    ]
    link_flags_plugin_vst3 = [
        '-Wl,-exported_symbol,_bundleEntry',
        '-Wl,-exported_symbol,_bundleExit',
        '-Wl,-exported_symbol,_GetPluginFactory',
    ]
else
    link_flags_plugin_lv2 = [
        '-Wl,--version-script=' + meson.source_root() + '/scripts/plugin-symbols-lv2.version',
    ]
    link_flags_plugin_vst2 = [
        '-Wl,--version-script=' + meson.source_root() + '/scripts/plugin-symbols-vst2.version',
    ]
    link_flags_plugin_vst3 = [
        '-Wl,--version-script=' + meson.source_root() + '/scripts/plugin-symbols-vst3.version',
    ]
endif

plugin_name = 'vitalium'
plugin_uses_opengl = true
plugin_dependencies = []
plugin_include_dirs = include_directories([
    '../libs/juce6',
    '../libs/juce6/source',
    '../libs/juce6/source/modules',
    '../libs/juce6/source/modules/juce_audio_processors/format_types/VST3_SDK',
    '../libs/juce-plugin',
    '.',
    'source/common',
    'source/common/wavetable',
    'source/interface/editor_components',
    'source/interface/editor_sections',
    'source/interface/look_and_feel',
    'source/interface/wavetable',
    'source/interface/wavetable/editors',
    'source/interface/wavetable/overlays',
    'source/plugin',
    'source/synthesis/synth_engine',
    'source/synthesis/effects',
    'source/synthesis/filters',
    'source/synthesis/framework',
    'source/synthesis/lookups',
    'source/synthesis/modulators',
    'source/synthesis/modules',
    'source/synthesis/producers',
    'source/synthesis/utilities',
    'third_party',
])

plugin_build_flags = [
    '-Wno-char-subscripts',
    '-Wno-deprecated-declarations',
    '-Wno-sign-compare',
    '-DNO_AUTH=1',
]

if buildtype == 'release'
    plugin_build_flags += [
        '-ftree-vectorize',
        '-ftree-slp-vectorize',
        '-funroll-loops',
        '-Ofast',
#         '-flto',
    ]
endif

if os_windows and host_machine.cpu() == 'x86'
    plugin_build_flags += [
        '-O0',
    ]
endif

plugin_link_flags = []
plugin_format_specific_srcs = []

if linux_embed
    plugin_srcs = files([
        'BinaryData.cpp',
        'source/unity_build/common.cpp',
        'source/unity_build/plugin.cpp',
        'source/unity_build/synthesis.cpp',
    ])
else
    plugin_srcs = files([
        'BinaryData.cpp',
        'source/unity_build/common.cpp',
        'source/unity_build/interface_editor_components.cpp',
        'source/unity_build/interface_editor_sections.cpp',
        'source/unity_build/interface_editor_sections2.cpp',
        'source/unity_build/interface_look_and_feel.cpp',
        'source/unity_build/interface_wavetable.cpp',
        'source/unity_build/plugin.cpp',
        'source/unity_build/synthesis.cpp',
    ])
endif

plugin_format_specific_srcs += files([
  '../libs/juce-plugin/JucePluginMain.cpp',
  '../libs/juce-plugin/JucePluginUtils.cpp',
])

if os_darwin
    plugin_link_flags += [
        '-framework', 'OpenGL',
    ]
elif os_windows
    plugin_dependencies += [
        cc.find_library('opengl32'),
    ]
elif not linux_embed
    plugin_dependencies += [
        dependency('gl')
    ]
endif

if os_windows
    build_flags_plugin_vst3 += [
        '-D_NATIVE_WCHAR_T_DEFINED',
        '-D__wchar_t=wchar_t',
    ]
endif

plugin_lib = static_library(plugin_name + '_lib',
  name_prefix: '',
  sources: plugin_srcs,
  include_directories: [
      plugin_include_dirs,
  ],
  c_args: build_flags + build_flags_plugin + plugin_build_flags,
  cpp_args: build_flags_cpp + build_flags_plugin + build_flag_plugin_cpp + plugin_build_flags,
  dependencies: plugin_dependencies,
  pic: true,
  install: false,
)

if build_lv2
  plugin_lv2_lib = shared_library(plugin_name + '_lv2',
      name_prefix: '',
      sources: plugin_format_specific_srcs,
      include_directories: [
          plugin_include_dirs,
      ],
      c_args: build_flags + build_flags_plugin + build_flags_plugin_lv2 + plugin_build_flags,
      cpp_args: build_flags_cpp + build_flags_plugin + build_flags_plugin_lv2 + build_flag_plugin_cpp + plugin_build_flags,
      link_args: link_flags + link_flags_plugin_common + link_flags_plugin_lv2 + plugin_link_flags,
      link_with: [ lib_juce6, plugin_lib ],
  )

  plugin_lv2_dir = meson.current_build_dir() / plugin_name + '.lv2'

  plugin_lv2_ttl = custom_target(plugin_name + '_lv2-ttl',
      output: plugin_name + '.lv2',
      input: plugin_lv2_lib,
      command: [
          'mkdir', '-p', plugin_lv2_dir, '&&',
          'cd', plugin_lv2_dir, '&&',
          'mv', plugin_lv2_lib.full_path(), plugin_lv2_dir / plugin_name + lib_suffix, '&&',
          (meson.is_cross_build() ? 'wine' : 'env'), lv2_ttl_generator, '.' / plugin_name + lib_suffix,
      ],
      install: true,
      install_dir: lv2dir,
  )
endif

if build_vst2
  plugin_vst2 = shared_library(plugin_name,
      name_prefix: '',
      sources: plugin_format_specific_srcs,
      include_directories: [
          plugin_include_dirs,
      ],
      c_args: build_flags + build_flags_plugin + build_flags_plugin_vst2 + plugin_build_flags,
      cpp_args: build_flags_cpp + build_flags_plugin + build_flags_plugin_vst2 + build_flag_plugin_cpp + plugin_build_flags,
      link_args: link_flags + link_flags_plugin_common + link_flags_plugin_vst2 + plugin_link_flags,
      link_with: [ lib_juce6, plugin_lib ],
      install: true,
      install_dir: vst2dir,
  )
endif

if build_vst3
  plugin_vst3_lib = shared_library(plugin_name + '-vst3',
      name_prefix: '',
      sources: plugin_format_specific_srcs,
      include_directories: [
          plugin_include_dirs,
      ],
      c_args: build_flags + build_flags_plugin + build_flags_plugin_vst3 + plugin_build_flags,
      cpp_args: build_flags_cpp + build_flags_plugin + build_flags_plugin_vst3 + build_flag_plugin_cpp + plugin_build_flags,
      link_args: link_flags + link_flags_plugin_common + link_flags_plugin_vst3 + plugin_link_flags,
      link_with: [ lib_juce6, plugin_lib ],
  )

  plugin_vst3_dir = meson.current_build_dir() / plugin_name + '.vst3' / 'Contents' / host_machine.cpu_family() + '-' + host_machine.system()

  plugin_vst3 = custom_target(plugin_name + '_vst3-bundle',
      output: plugin_name + '.vst3',
      input: plugin_vst3_lib,
      command: [
          'mkdir', '-p', plugin_vst3_dir, '&&',
          'cd', plugin_vst3_dir, '&&',
          'mv', plugin_vst3_lib.full_path(), plugin_vst3_dir / plugin_name + lib_suffix,
      ],
      install: true,
      install_dir: vst3dir,
  )
endif

