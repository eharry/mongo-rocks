# -*- mode: python -*-
Import("env")

env = env.Clone()

dynamic_syslibdeps = []
conf = Configure(env)

if conf.CheckLibWithHeader("lz4", ["lz4.h","lz4hc.h"], "C", "LZ4_versionNumber();", autoadd=False ):
    dynamic_syslibdeps.append("lz4")

conf.Finish()

env.InjectMongoIncludePaths()

env.Library(
    target= 'storage_rocks_base',
    source= [
        'src/rocks_compaction_scheduler.cpp',
        'src/rocks_counter_manager.cpp',
        'src/rocks_global_options.cpp',
        'src/rocks_engine.cpp',
        'src/rocks_record_store.cpp',
        'src/rocks_recovery_unit.cpp',
        'src/rocks_index.cpp',
        'src/rocks_durability_manager.cpp',
        'src/rocks_snapshot_manager.cpp',
        'src/rocks_util.cpp',
        'src/rocks_oplog_manager.cpp',
        'src/rocks_begin_transaction_block.cpp',
        'src/rocks_prepare_conflict.cpp',
        'src/mongo_rate_limiter_checker.cpp',
    ],
    LIBDEPS= [
        '$BUILD_DIR/mongo/base',
        '$BUILD_DIR/mongo/db/namespace_string',
        '$BUILD_DIR/mongo/db/commands/test_commands_enabled',
        '$BUILD_DIR/mongo/db/catalog/collection_options',
        '$BUILD_DIR/mongo/db/concurrency/lock_manager',
        '$BUILD_DIR/mongo/db/concurrency/write_conflict_exception',
        '$BUILD_DIR/mongo/db/index/index_descriptor',
        '$BUILD_DIR/mongo/db/storage/bson_collection_catalog_entry',
        '$BUILD_DIR/mongo/db/storage/index_entry_comparison',
        '$BUILD_DIR/mongo/db/storage/journal_listener',
        '$BUILD_DIR/mongo/db/storage/key_string',
        '$BUILD_DIR/mongo/db/storage/oplog_hack',
        '$BUILD_DIR/mongo/util/background_job',
        '$BUILD_DIR/mongo/util/concurrency/ticketholder',
        '$BUILD_DIR/mongo/util/processinfo',
        '$BUILD_DIR/third_party/shim_snappy',
    ],
    SYSLIBDEPS=["rocksdb",
                "z"] #z are dependencies for rocks
               + dynamic_syslibdeps
)

env.Library(
    target= 'storage_rocks',
    source= [
        'src/rocks_init.cpp',
        'src/rocks_options_init.cpp',
        'src/rocks_parameters.cpp',
        'src/rocks_record_store_mongod.cpp',
        'src/rocks_server_status.cpp',
        ],
    LIBDEPS= [
        'storage_rocks_base',
        '$BUILD_DIR/mongo/db/storage/kv/kv_engine'
        ],
    LIBDEPS_DEPENDENTS=['$BUILD_DIR/mongo/db/serveronly']
)

env.Library(
    target= 'storage_rocks_mock',
    source= [
        'src/rocks_record_store_mock.cpp',
        ],
    LIBDEPS= [
        'storage_rocks_base',
        # Temporary crutch since the ssl cleanup is hard coded in background.cpp
        '$BUILD_DIR/mongo/util/net/network',
    ]
)


env.CppUnitTest(
   target='storage_rocks_index_test',
   source=['src/rocks_index_test.cpp'
           ],
   LIBDEPS=[
        'storage_rocks_mock',
        '$BUILD_DIR/mongo/db/storage/sorted_data_interface_test_harness'
   ]
)


env.CppUnitTest(
   target='storage_rocks_record_store_test',
   source=[
        'src/rocks_record_store_test.cpp'
   ],
   LIBDEPS=[
        '$BUILD_DIR/mongo/db/auth/authmocks',
        '$BUILD_DIR/mongo/db/storage/record_store_test_harness',
        '$BUILD_DIR/mongo/db/repl/repl_coordinator_interface',
        '$BUILD_DIR/mongo/db/repl/replmocks',
        '$BUILD_DIR/mongo/util/clock_source_mock',
        'storage_rocks_mock',
   ]
)

env.CppUnitTest(
    target='storage_rocks_recovery_unit_test',
    source=[
        'src/rocks_recovery_unit_test.cpp',
    ],
    LIBDEPS=[
        'storage_rocks_mock',
        '$BUILD_DIR/mongo/util/clock_source_mock',
        '$BUILD_DIR/mongo/db/storage/test_harness_helper',
    ],
    LIBDEPS_PRIVATE=[
        '$BUILD_DIR/mongo/db/auth/authmocks',
        '$BUILD_DIR/mongo/db/index/index_access_methods',
        '$BUILD_DIR/mongo/db/repl/repl_coordinator_interface',
        '$BUILD_DIR/mongo/db/repl/replmocks',
    ],
)
