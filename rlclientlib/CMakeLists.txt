set(PROJECT_SOURCES
  api_status.cc
  async_batcher.cc
  azure_factories.cc
  console_tracer.cc
  error_callback_fn.cc
  factory_resolver.cc
  live_model_impl.cc
  live_model.cc
  logger/async_batcher.cc
  logger/event_logger.cc
  logger/eventhub_client.cc
  model_mgmt/data_callback_fn.cc
  model_mgmt/model_downloader.cc
  model_mgmt/model_mgmt.cc
  model_mgmt/restapi_data_transport.cc
  ranking_event.cc
  ranking_response.cc
  trace_logger.cc
  utility/config_utility.cc
  utility/configuration.cc
  utility/context_helper.cc
  utility/data_buffer.cc
  utility/str_util.cc
  utility/watchdog.cc
  vw_model/safe_vw.cc
  vw_model/vw_model.cc
)

set(PROJECT_PUBLIC_HEADERS
  ../include/action_flags.h
  ../include/api_status.h
  ../include/config_utility.h
  ../include/configuration.h
  ../include/constants.h
  ../include/err_constants.h
  ../include/factory_resolver.h
  ../include/live_model.h
  ../include/model_mgmt.h
  ../include/object_factory.h
  ../include/personalization.h
  ../include/ranking_response.h
  ../include/sender.h
  ../include/str_util.h
  ../include/trace_logger.h
)

set(PROJECT_PRIVATE_HEADERS
  azure_factories.h
  console_tracer.h
  error_callback_fn.h
  live_model_impl.h
  logger/async_batcher.h
  logger/event_logger.h
  logger/eventhub_client.h
  model_mgmt/data_callback_fn.h
  model_mgmt/model_downloader.h
  model_mgmt/restapi_data_transport.h
  moving_queue.h
  ranking_event.h
  utility/context_helper.h
  utility/data_buffer.h
  utility/http_helper.h
  utility/interruptable_sleeper.h
  utility/object_pool.h
  utility/periodic_background_proc.h
  utility/watchdog.h
  vw_model/safe_vw.h
  vw_model/vw_model.h
)

source_group("Sources" FILES ${PROJECT_SOURCES})
source_group("Public headers" FILES ${PROJECT_PUBLIC_HEADERS})
source_group("Private headers" FILES ${PROJECT_PRIVATE_HEADERS})

add_library(rlclientlib ${PROJECT_SOURCES} ${PROJECT_PUBLIC_HEADERS} ${PROJECT_PRIVATE_HEADERS})
set_target_properties(rlclientlib PROPERTIES POSITION_INDEPENDENT_CODE ON)
target_include_directories(rlclientlib PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/../include PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(rlclientlib PUBLIC Boost::system vw OpenSSL::SSL OpenSSL::Crypto cpprestsdk::cpprest)

# Consuming Boost uuid requires BCrypt, normally this is automatically linked but vcpkg turns this feature off.
if(WIN32)
  target_link_libraries(rlclientlib PUBLIC bcrypt)
endif()

if(TURN_OFF_DEVIRTUALIZE)
  target_compile_options(rlclientlib PUBLIC -fno-devirtualize)
endif()

install(TARGETS rlclientlib
  INCLUDES DESTINATION rlclientlib
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
)