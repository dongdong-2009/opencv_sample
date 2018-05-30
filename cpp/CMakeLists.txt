# remove all matching elements from the list
macro(ocv_list_filterout lst regex)
  foreach(item ${${lst}})
    if(item MATCHES "${regex}")
      list(REMOVE_ITEM ${lst} "${item}")
    endif()
  endforeach()
endmacro()

#   ocv_define_sample(<output target> <relative filename> <group>)
function(ocv_define_sample out_target source sub)
  get_filename_component(name "${source}" NAME_WE)
  set(the_target "example_${sub}_${name}")
  add_executable(${the_target} "${source}")
  set_target_properties(${the_target} PROPERTIES PROJECT_LABEL "${name}")
  set_target_properties(${the_target} PROPERTIES FOLDER "samples/${sub}")
  if(WIN32 AND MSVC AND NOT BUILD_SHARED_LIBS)
    set_target_properties(${the_target} PROPERTIES LINK_FLAGS "/NODEFAULTLIB:atlthunk.lib /NODEFAULTLIB:atlsd.lib /DEBUG")
  endif()
  if(WIN32)
    install(TARGETS ${the_target} RUNTIME DESTINATION "samples/${sub}" COMPONENT samples)
  endif()
  # Add single target to build all samples in the group: 'make opencv_samples_cpp'
  set(parent_target opencv_samples_${sub})
  if(NOT TARGET ${parent_target})
    add_custom_target(${parent_target})
    if(TARGET opencv_samples)
      add_dependencies(opencv_samples ${parent_target})
    endif()
  endif()
  add_dependencies(${parent_target} ${the_target})
  set(${out_target} ${the_target} PARENT_SCOPE)
endfunction()

set(LINK_LIBRARIES
	opencv_aruco400d
	opencv_bgsegm400d
	opencv_bioinspired400d
	opencv_calib3d400d
	opencv_ccalib400d
	opencv_core400d
	opencv_datasets400d
	opencv_dnn_objdetect400d
	opencv_dnn400d
	opencv_dpm400d
	opencv_face400d
	opencv_features2d400d
	opencv_flann400d
	opencv_fuzzy400d
	opencv_hfs400d
	opencv_highgui400d
	opencv_img_hash400d
	opencv_imgcodecs400d
	opencv_imgproc400d
	opencv_line_descriptor400d
	opencv_ml400d
	opencv_objdetect400d
	opencv_optflow400d
	opencv_phase_unwrapping400d
	opencv_photo400d
	opencv_plot400d
	opencv_reg400d
	opencv_rgbd400d
	opencv_saliency400d
	opencv_shape400d
	opencv_stereo400d
	opencv_stitching400d
	opencv_structured_light400d
	opencv_superres400d
	opencv_surface_matching400d
	opencv_text400d
	opencv_tracking400d
	opencv_video400d
	opencv_videoio400d
	opencv_videostab400d
	opencv_xfeatures2d400d
	opencv_ximgproc400d
	opencv_xobjdetect400d
	opencv_xphoto400d
)

link_libraries(${LINK_LIBRARIES})

cmake_minimum_required(VERSION 3.2)
project(cpp_samples)
file(GLOB_RECURSE cpp_samples RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} *.cpp)

ocv_list_filterout(cpp_samples Qt_sample)
ocv_list_filterout(cpp_samples "/gpu/")
ocv_list_filterout(cpp_samples "/viz/")
ocv_list_filterout(cpp_samples "/ippasync/")
ocv_list_filterout(cpp_samples "real_time_pose_estimation/")
SET_PROPERTY(GLOBAL PROPERTY USE_FOLDERS ON)

foreach(sample_filename ${cpp_samples})
  set(package "cpp")
  if(sample_filename MATCHES "tutorial_code")
    set(package "tutorial")
  endif()
  ocv_define_sample(tgt ${sample_filename} ${package})
endforeach()