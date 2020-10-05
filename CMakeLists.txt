cmake_minimum_required(VERSION 3.15)
project(wxFormBuilder)

find_package(wxWidgets COMPONENTS adv aui core gl html media net propgrid qa ribbon richtext stc xml xrc base REQUIRED)
include(${wxWidgets_USE_FILE})


file(COPY "${CMAKE_CURRENT_SOURCE_DIR}/output/" DESTINATION "${CMAKE_CURRENT_BINARY_DIR}")

#
# ticpp
#

add_library(ticpp STATIC
        subprojects/ticpp/ticpp.cpp
        subprojects/ticpp/tinystr.cpp
        subprojects/ticpp/tinyxml.cpp
        subprojects/ticpp/tinyxmlerror.cpp
        subprojects/ticpp/tinyxmlparser.cpp
        )

target_compile_definitions(ticpp
        PUBLIC TIXML_USE_TICPP
        )

target_include_directories(ticpp
        PUBLIC subprojects/ticpp
        )

#
# plugin_interface
#

add_library(plugin_interface STATIC
        sdk/plugin_interface/xrcconv.cpp
        sdk/plugin_interface/forms/wizard.cpp
        )

target_compile_definitions(plugin_interface
        PUBLIC TIXML_USE_TICPP
        )

target_include_directories(plugin_interface
        PUBLIC sdk/plugin_interface
        )

target_link_libraries(plugin_interface
        PUBLIC ticpp
        )

#
# libcommon
#

add_library(libcommon SHARED
        plugins/common/common.cpp
        )

target_link_libraries(libcommon
        PRIVATE plugin_interface
        PRIVATE ${wxWidgets_LIBRARIES}
        )

target_compile_definitions(libcommon
        PRIVATE BUILD_DLL
        )

set_target_properties(libcommon
        PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/plugins/common"
        )

#
# libcontainers
#

add_library(libcontainers SHARED
        plugins/containers/containers.cpp
        )

target_link_libraries(libcontainers
        PRIVATE plugin_interface
        PRIVATE ${wxWidgets_LIBRARIES}
        )

target_compile_definitions(libcontainers
        PRIVATE BUILD_DLL
        )

set_target_properties(libcontainers
        PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/plugins/containers"
        )

#
# libforms
#

add_library(libforms SHARED
        plugins/forms/forms.cpp
        )

target_link_libraries(libforms
        PRIVATE plugin_interface
        PRIVATE ${wxWidgets_LIBRARIES}
        )

target_compile_definitions(libforms
        PRIVATE BUILD_DLL
        )

set_target_properties(libforms
        PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/plugins/forms"
        )

#
# liblayout
#

add_library(liblayout SHARED
        plugins/layout/layout.cpp
        )

target_link_libraries(liblayout
        PRIVATE plugin_interface
        PRIVATE ${wxWidgets_LIBRARIES}
        )

target_compile_definitions(liblayout
        PRIVATE BUILD_DLL
        )

set_target_properties(liblayout
        PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/plugins/layout"
        )

#
# libadditional
#

add_library(libadditional SHARED
        plugins/additional/additional.cpp
        )

target_link_libraries(libadditional
        PRIVATE plugin_interface
        PRIVATE ${wxWidgets_LIBRARIES}
        )

target_compile_definitions(libadditional
        PRIVATE BUILD_DLL
        )

set_target_properties(libadditional
        PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/plugins/additional"
        )

#
# wxFormBuilder
#

add_executable(wxFormBuilder WIN32
        src/codegen/codegen.cpp
        src/codegen/codeparser.cpp
        src/codegen/codewriter.cpp
        src/codegen/cppcg.cpp
        src/codegen/luacg.cpp
        src/codegen/phpcg.cpp
        src/codegen/pythoncg.cpp
        src/codegen/xrccg.cpp
        src/maingui.cpp
        src/md5/md5.cc
        src/model/database.cpp
        src/model/objectbase.cpp
        src/model/types.cpp
        src/model/xrcfilter.cpp
        src/rad/about.cpp
        src/rad/appdata.cpp
        src/rad/bitmaps.cpp
        src/rad/cmdproc.cpp
        src/rad/codeeditor/codeeditor.cpp
        src/rad/cpppanel/cpppanel.cpp
        src/rad/dataobject/dataobject.cpp
        src/rad/designer/innerframe.cpp
        src/rad/designer/menubar.cpp
        src/rad/designer/visualeditor.cpp
        src/rad/designer/visualobj.cpp
        src/rad/geninheritclass/geninhertclass_gui.cpp
        src/rad/geninheritclass/geninhertclass.cpp
        src/rad/inspector/objinspect.cpp
        src/rad/inspector/wxfbadvprops.cpp
        src/rad/luapanel/luapanel.cpp
        src/rad/mainframe.cpp
        src/rad/menueditor.cpp
        src/rad/objecttree/objecttree.cpp
        src/rad/palette.cpp
        src/rad/phppanel/phppanel.cpp
        src/rad/pythonpanel/pythonpanel.cpp
        src/rad/title.cpp
        src/rad/wxfbevent.cpp
        src/rad/wxfbmanager.cpp
        src/rad/xrcpanel/xrcpanel.cpp
        src/rad/xrcpreview/xrcpreview.cpp
        src/utils/annoyingdialog.cpp
        src/utils/filetocarray.cpp
        src/utils/stringutils.cpp
        src/utils/typeconv.cpp
        src/utils/wxfbipc.cpp
        )

target_link_libraries(wxFormBuilder
        PRIVATE plugin_interface
        PRIVATE ${wxWidgets_LIBRARIES}
        )

add_dependencies(wxFormBuilder
        libcommon
        libcontainers
        libforms
        liblayout
        libadditional
        )