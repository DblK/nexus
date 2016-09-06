# nexus


A Dockerfile for Sonatype Nexus Repository Manager 3, based on Alpine.

To run, binding the exposed port 8081 to the host.

```
$ docker run -d -p 8081:8081 --name nexus clearent/nexus
```


## Notes

* Default credentials are: `admin` / `admin123`

* It can take some time (2-3 minutes) for the service to launch in a
new container.  You can tail the log to determine once Nexus is ready:

```
$ docker logs -f nexus
```

* Installation of Nexus is to `/opt/nexus`.  

* A persistent directory, `/nexus-data`, is used for configuration,
logs, and storage.

* Two environment variables can be used to control the JVM arguments

  * `JAVA_MAX_MEM`, passed as -Xmx.  Defaults to `1200m`.

  * `JAVA_MIN_MEM`, passed as -Xms.  Defaults to `1200m`.

  These can be used supplied at runtime to control the JVM:

  ```
  $ docker run -d -p 8081:8081 --name nexus -e JAVA_MAX_MEM=2048m clearent/nexus
  ```


### Persistent Data

There are two general approaches to handling persistent storage requirements
with Docker. See [Managing Data in Containers](https://docs.docker.com/userguide/dockervolumes/)
for additional information.

  1. *Use a data volume container*.  Since data volumes are persistent
  until no containers use them, a container can created specifically for 
  this purpose.  This is the recommended approach.  

  ```
  $ docker run -d --name nexus-data clearent/nexus echo "data-only container for Nexus"
  $ docker run -d -p 8081:8081 --name nexus --volumes-from nexus-data clearent/nexus
  ```

  2. *Mount a host directory as the volume*.

  ```
  $ docker run -d -p 8081:8081 --name nexus -v /some/dir/nexus-data:/nexus-data clearent/nexus
  ```