# project name and programming language
project('com.github.elfenware.badger', 'vala', 'c', version: '5.0.3')

gnome = import('gnome')
i18n = import('i18n')

add_global_arguments('-DGETTEXT_PACKAGE="@0@"'.format (meson.project_name()), language:'c')


#================================
# Import the stylesheet

## Set de config data
config_data = configuration_data()
config_data.set_quoted('LOCALEDIR', get_option('prefix') / get_option('localedir'))
config_data.set_quoted('GETTEXT_PACKAGE', meson.project_name())
config_file = configure_file(
    input: 'src/Config.vala.in',
    output: '@BASENAME@',
    configuration: config_data
)

# Le stylesheet
# Use find_program with native: true so cross builds (e.g. Windows via msys2)
# use the host glib-compile-resources instead of the broken gio-2.0 tool
# variable that points at a nonexistent path.
glib_compile_resources = find_program('glib-compile-resources', native: true)

gresource_c = custom_target(
    'gresource_c',
    input: 'data' / 'gresource.xml',
    output: 'gresource.c',
    command: [
        glib_compile_resources,
        '@INPUT@',
        '--sourcedir', meson.current_source_dir() / 'data',
        '--generate-source',
        '--target', '@OUTPUT@',
        '--dependency-file', '@DEPFILE@',
    ],
    depfile: 'gresource.c.d',
)

gresource_h = custom_target(
    'gresource_h',
    input: 'data' / 'gresource.xml',
    output: 'gresource.h',
    command: [
        glib_compile_resources,
        '@INPUT@',
        '--sourcedir', meson.current_source_dir() / 'data',
        '--generate-header',
        '--target', '@OUTPUT@',
    ],
)

gresource = [gresource_c, gresource_h]

executable(
    meson.project_name(),
    gresource,
    config_file,
    'src/Application.vala',
    'src/MainWindow.vala',
    'src/Reminder.vala',
    'src/MainGrid.vala',
    'src/Marks.vala',
    dependencies: [
        dependency ('glib-2.0'),
        dependency('granite-7'),
        dependency('gobject-2.0'),
        dependency('gtk4'),
        dependency('libportal')
    ],
    install : true
)

# Add subfolders for Meson to look
subdir('data')
subdir('po')

gnome.post_install(
  glib_compile_schemas: true,
  gtk_update_icon_cache: true,
  update_desktop_database: true,
  )

