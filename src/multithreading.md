# Multithreaded analysis

The trajectory analysis can be performed using multiple threads. Utilizing more threads is generally much faster than using just one, although the speed gain also depends on the read performance of your storage device.

By default, `gorder` uses only 1 thread to perform the analysis. If you want to speed up the process by using multiple threads, you can specify the number of threads in your configuration YAML file:

```yaml
n_threads: 4
```

In the example above, the analysis will be performed using 4 threads.

> `gorder` guarantees binary-identical results on the same system and build environment, independent of the number of threads used.