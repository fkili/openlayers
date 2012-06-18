cmake_minimum_required(VERSION 2.8)

PROJECT(openlayers)

SET (PLUGIN_NAME openlayers)

INCLUDE(../../config/localConfig.txt)
INCLUDE(../../config/commonConfig.txt)
INCLUDE(../../config/PyQt4Macros.cmake.txt)

SET(PLUGIN_FILES
   __init__.py
   bindogr.py
   openlayers_layer.py
   openlayers_ovwidget.py
   openlayers_plugin.py
   openlayers_plugin_layer_type.py
   resources.py
   resources_rc.py
   tools_network.py
   apple_icon.png
   bing_icon.png
   google_icon.png
   kml.png
   mActionAddRasterLayer.png
   mActionSaveMapAsImage.png
   mActionDraw.png
   openlayers.png
   osm_icon.png
   x.png
   yahoo_icon.png
)
SET(openlayers_UIS 
   openlayers_ovwidgetbase.ui
)
SET(DIR
	html
	)
PYQT4_WRAP_UI (openlayers_PYUIS ${openlayers_UIS} )
PYQT4_ADD_RESOURCES(openlayers_QRC resources_tr.qrc resources.qrc)
ADD_CUSTOM_TARGET(openlayers ALL DEPENDS  ${openlayers_QRC} ${openlayers_PYUIS})

########################################################
# QGIS PYTHON PLUGIN INSTALLER BUILDING

## Folder creation
IF(NOT TARGET ALL-SETUPS )
	SET(TOP_DIR ${CMAKE_CURRENT_SOURCE_DIR})
ENDIF()

SET(SETUP_SOURCE ${CMAKE_CURRENT_BINARY_DIR}/${PLUGIN_NAME})
SET(SETUP_ZIP ${TOP_DIR}/src/repository/plugins/${PLUGIN_NAME}.zip)

# DOS COMMANDS NEED WINDOWS PATHS
file(TO_NATIVE_PATH ${SETUP_SOURCE} SETUP_SOURCE_NATIVE)
file(TO_NATIVE_PATH ${SETUP_SOURCE}/${DIR} SETUP_SOURCE_DIR_NATIVE)
INSTALL(FILES ${${PLUGIN_NAME}_PYUIS} DESTINATION ${SETUP_SOURCE})
INSTALL(FILES ${${PLUGIN_NAME}_QRC}   DESTINATION ${SETUP_SOURCE})
INSTALL(FILES ${PLUGIN_FILES}         DESTINATION ${SETUP_SOURCE})
INSTALL(FILES ${PYTHON_LIB}         DESTINATION ${SETUP_SOURCE})

## Zip created folder
ADD_CUSTOM_COMMAND( OUTPUT ${SETUP_ZIP}
      DEPENDS ${SETUP_SOURCE}
	  WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
      COMMAND mkdir
      ARGS ${SETUP_SOURCE_NATIVE}
      COMMAND cp
      ARGS ${PLUGIN_FILES} ${SETUP_SOURCE_NATIVE}
	  COMMAND xcopy
      ARGS /I /S ${DIR} ${SETUP_SOURCE_DIR_NATIVE}
      WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR} 	  
      COMMAND cp
      ARGS ${${PLUGIN_NAME}_PYUIS} ${SETUP_SOURCE_NATIVE}
      COMMAND cp
      ARGS ${${PLUGIN_NAME}_QRC} ${SETUP_SOURCE_NATIVE}  
	  WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}      
      COMMAND "C:/Program Files/7-Zip/7z.exe"
      ARGS a -tzip ${SETUP_ZIP} ${SETUP_SOURCE_NATIVE}
	  WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
      COMMAND rmdir
      ARGS /Q /S ${SETUP_SOURCE_NATIVE}

)

ADD_CUSTOM_TARGET(${PLUGIN_NAME}-SETUP  DEPENDS ${SETUP_ZIP} )
add_dependencies( ${PLUGIN_NAME}-SETUP depend ${PLUGIN_NAME} )
# global build ?
IF(TARGET ALL-SETUPS )
   add_dependencies( ALL-SETUPS depend ${PLUGIN_NAME}-SETUP )
ENDIF()
