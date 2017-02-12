# Not working, yet!
```
Java HotSpot(TM) Server VM warning: INFO: os::commit_memory(0x41e00000, 838860800, 0) failed; error='Cannot allocate memory' (errno=12)
#
# There is insufficient memory for the Java Runtime Environment to continue.
# Native memory allocation (mmap) failed to map 838860800 bytes for committing reserved memory.
# An error report file with more information is saved as:
# /tmp/hs_err_pid8.log
```

# Sonatype Nexus3 Docker: marhan/rpi-nexus

A Dockerfile for Sonatype Nexus Repository Manager 3, based on Raspbian Jessie.

* [Notes](#notes)
  * [Persistent Data](#persistent-data)
  * [Build Args](#build-args)
* [Getting Help](#getting-help)

To run, binding the exposed port 8081 to the host.

```
$ docker run -d -p 8081:8081 --name nexus marhan/rpi-nexus
```

To test:

```
$ curl -u admin:admin123 http://localhost:8081/service/metrics/ping
```

To (re)build the image:

Copy the Dockerfile and do the build-

```
$ make build
```

## Notes

* Default credentials are: `admin` / `admin123`

* It can take some time (2-3 minutes) for the service to launch in a
new container.  You can tail the log to determine once Nexus is ready:

```
$ docker logs -f nexus
```

* Installation of Nexus is to `/opt/sonatype/nexus`.  

* A persistent directory, `/nexus-data`, is used for configuration,
logs, and storage. This directory needs to be writable by the Nexus
process, which runs as UID 200.

* Three environment variables can be used to control the JVM arguments

  * `JAVA_MAX_MEM`, passed as -Xmx.  Defaults to `800m`.

  * `JAVA_MIN_MEM`, passed as -Xms.  Defaults to `800m`.

  * `EXTRA_JAVA_OPTS`.  Additional options can be passed to the JVM via
  this variable.

  These can be used supplied at runtime to control the JVM:

  ```
  $ docker run -d -p 8081:8081 --name nexus -e JAVA_MAX_MEM=768m marhan/rpi-nexus
  ```

* Another environment variable can be used to control the Nexus Context Path

  * `NEXUS_CONTEXT`, defaults to /

  This can be supplied at runtime:

  ```
  $ docker run -d -p 8081:8081 --name nexus -e NEXUS_CONTEXT=nexus marhan/rpi-nexus
  ```

### Persistent Data

There are two general approaches to handling persistent storage requirements
with Docker. See [Managing Data in Containers](https://docs.docker.com/engine/tutorials/dockervolumes/)
for additional information.

  1. *Use a data volume*.  Since data volumes are persistent
  until no containers use them, a volume can be created specifically for
  this purpose.  This is the recommended approach.  

  ```
  $ docker volume create --name nexus-data
  $ docker run -d -p 8081:8081 --name nexus -v nexus-data:/nexus-data marhan/rpi-nexus
  ```

  2. *Mount a host directory as the volume*.  This is not portable, as it
  relies on the directory existing with correct permissions on the host.
  However it can be useful in certain situations where this volume needs
  to be assigned to certain specific underlying storage.  

  ```
  $ mkdir /some/dir/nexus-data && chown -R 200 /some/dir/nexus-data
  $ docker run -d -p 8081:8081 --name nexus -v /some/dir/nexus-data:/nexus-data marhan/rpi-nexus
  ```

## Getting Help

Looking to contribute to our Docker image but need some help? There's a few ways to get information or our attention:

* File a public issue [here on GitHub](https://github.com/sonatype/docker-nexus3/issues)
* Check out the [Nexus3](http://stackoverflow.com/questions/tagged/nexus3) tag on Stack Overflow
* Check out the [Nexus Repository User List](https://groups.google.com/a/glists.sonatype.com/forum/?hl=en#!forum/nexus-users)
