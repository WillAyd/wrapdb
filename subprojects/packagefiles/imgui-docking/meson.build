project(
  'imgui',
  'cpp',
  license: 'MIT',
  version: '1.91.6',
  meson_version: '>=0.50.0',
)

if host_machine.system() == 'darwin'
  add_languages('objcpp')
endif

include_dirs = include_directories('.', 'backends')
sources = files(
  'imgui.cpp',
  'imgui_demo.cpp',
  'imgui_draw.cpp',
  'imgui_tables.cpp',
  'imgui_widgets.cpp',
  'misc/cpp/imgui_stdlib.cpp',
)

cpp = meson.get_compiler('cpp')
dependencies = []

# renderer backends
dependencies += cpp.find_library(
  'd3dcompiler',
  required: host_machine.system() == 'windows',
)
dx9_dep = cpp.find_library(
  'd3d9',
  required: get_option('dx9'),
)
if dx9_dep.found()
  sources += 'backends/imgui_impl_dx9.cpp'
  dependencies += dx9_dep
endif
dx10_dep = cpp.find_library(
  'd3d10',
  required: get_option('dx10'),
)
if dx10_dep.found()
  sources += 'backends/imgui_impl_dx10.cpp'
  dependencies += dx10_dep
endif
dx11_dep = cpp.find_library(
  'd3d11',
  required: get_option('dx11'),
)
if dx11_dep.found()
  sources += 'backends/imgui_impl_dx11.cpp'
  dependencies += dx11_dep
endif
dx12_dep = cpp.find_library(
  'd3d12',
  required: get_option('dx12'),
)
# MinGW does not work. See https://github.com/ocornut/imgui/pull/4604
if dx12_dep.found() and cpp.get_argument_syntax() == 'msvc'
  sources += 'backends/imgui_impl_dx12.cpp'
  dependencies += dx12_dep
endif
dxgi_dep = cpp.find_library(
  'dxgi',
  required: get_option('dx12'),
)
if dxgi_dep.found() and cpp.get_argument_syntax() == 'msvc'
  dependencies += dxgi_dep
endif

metal_dep = dependency(
  'appleframeworks',
  modules: ['Foundation', 'AppKit', 'GameController', 'Metal', 'QuartzCore'],
  required: get_option('metal'),
)
if metal_dep.found()
  sources += 'backends/imgui_impl_metal.mm'
  dependencies += metal_dep
endif
libgl_dep = dependency(
  'gl',
  required: get_option('opengl'),
)
if libgl_dep.found()
  sources += 'backends/imgui_impl_opengl3.cpp'
  dependencies += libgl_dep
  dependencies += cpp.find_library(
    'dl',
    required: false,
  )
endif
sdl2_renderer_dep = dependency(
  'sdl2',
  version: '>=2.0.17',
  required: get_option('sdl_renderer'),
)
if sdl2_renderer_dep.found()
  sources += 'backends/imgui_impl_sdlrenderer2.cpp'
  dependencies += sdl2_renderer_dep
endif
vulkan_dep = dependency(
  'vulkan',
  required: get_option('vulkan'),
)
if vulkan_dep.found()
  sources += 'backends/imgui_impl_vulkan.cpp'
  dependencies += vulkan_dep
endif
if cpp.has_header(
  'webgpu/webgpu.h',
  required: get_option('webgpu'),
)
  sources += 'backends/imgui_impl_wgpu.cpp'
endif

# platform backends
glfw_dep = dependency(
  'glfw3',
  required: get_option('glfw'),
)
if glfw_dep.found()
  sources += 'backends/imgui_impl_glfw.cpp'
  dependencies += glfw_dep
endif
sdl2_dep = dependency(
  'sdl2',
  required: get_option('sdl2'),
)
if sdl2_dep.found()
  sources += 'backends/imgui_impl_sdl2.cpp'
  dependencies += sdl2_dep
endif
osx_dep = dependency(
  'appleframeworks',
  modules: ['Carbon', 'Cocoa', 'GameController'],
  required: get_option('osx'),
)
if osx_dep.found()
  sources += 'backends/imgui_impl_osx.mm'
endif
win_dep = cpp.find_library(
  'dwmapi',
  required: get_option('win'),
)
if win_dep.found()
  sources += 'backends/imgui_impl_win32.cpp'
  dependencies += win_dep
endif

# frameworks
allegro5_dep = dependency(
  'allegro-5',
  required: get_option('allegro5'),
)
allegro5_primitives_dep = dependency(
  'allegro_primitives-5',
  required: get_option('allegro5'),
)
if allegro5_dep.found() and allegro5_primitives_dep.found()
  sources += 'backends/imgui_impl_allegro5.cpp'
  dependencies += [allegro5_dep, allegro5_primitives_dep]
endif

api = '-DIMGUI_API=__attribute__((visibility("default")))'
if host_machine.system() == 'windows'
  api = '-DIMGUI_API=@0@'.format(
    get_option('default_library') != 'static' ? '__declspec(dllexport)' : '',
  )
endif

imgui = library(
  'imgui',
  sources,
  cpp_args: api,
  gnu_symbol_visibility: 'hidden',
  dependencies: dependencies,
  include_directories: include_dirs,
  install: true,
)

if host_machine.system() == 'windows'
  api = '-DIMGUI_API=@0@'.format(
    get_option('default_library') != 'static' ? '__declspec(dllimport)' : '',
  )
endif

imgui_dep = declare_dependency(
  compile_args: api,
  include_directories: include_dirs,
  link_with: imgui,
)
