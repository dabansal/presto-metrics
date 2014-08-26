# Presto::Metrics

Presto is a distributed SQL engine, which launches coordinator and worker servers to process distributed queries in a cluster. We need to monitor the states of these coordinators/workers. Presto::Metrics is a library for accessing these states from Ruby.

Presto provides REST API for accessing JMX properties. Presto::Metrics accesses this REST API to extract JMX property values.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'presto-metrics'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install presto-metrics

## Usage

```ruby

require 'presto/metrics'

client = Presto::Metrics::Client.new  # Access to http://localhost:8080 in default

# Alternatively, you can specify the host name and port number to use
client = Presto::Metrics::Client.new(:host => "localhost", :port=>8081) 


client.os_metrics
# => {:open_file_descriptor_count=>360, :max_file_descriptor_count=>10240, :committed_virtual_memory_size=>18683629568, :total_swap_space_size=>2147483648, :free_swap_space_size=>1132986368, :process_cpu_time=>240244441000, :free_physical_memory_size=>2088931328, :total_physical_memory_size=>17179869184, :system_cpu_load=>0.044989775051124746, :process_cpu_load=>0.002293214043176635, :name=>"Mac OS X", :version=>"10.9.4", :available_processors=>8, :arch=>"x86_64", :system_load_average=>2.0537109375, :object_name=>"java.lang:type=OperatingSystem"}

# This is equivalent to write as follows
client.get_metrics("java.lang:type=OperatingSystem")

# Retrieve a set of parameters
client.query_manager_metrics(["executor.active_count", "executor.completed_task_count"])
# => {:"executor.active_count"=>0, :"executor.completed_task_count"=>0}
client.os_metrics([:system_load_average, :free_physical_memory_size])
#=> {:free_physical_memory_size=>3690512384, :system_load_average=>2.33056640625}

# Retrieve standard metrics
client.memory_usage_metrics      # java.lang:Type=Memory
client.os_metrics                # java.lang:type=OperatingSystm
client.gc_cms_metrics            # java.lang:type=GarbageCollector,name=ConcurrentMarkSweep
client.gc_parnew_metrics         # java.lang:type=GarbageCollector,name=ParNew
client.query_manager_metrics     # com.facebook.presto.execution:name=QueryManager
client.query_execution_metrics   # com.facebook.presto.execution:name=QueryExecution
client.node_scheduler_metrics    # com.facebook.presto.execution:name=NodeScheduler
client.task_executor_metrics     # com.facebook.presto.execution:name=TaskExecutor
client.task_manager_metrics      # com.facebook.presto.execution:name=TaskManager

# Retrieve the JSON representation of JMX properties
client.get_json("java.lang:Type=Memory")

```

## Contributing

1. Fork it ( https://github.com/[my-github-username]/presto-metrics/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request
