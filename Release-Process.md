1. Modify DependencyBank.java, update the libgdx version to the latest (must match Version.java)
2. Build libGDX on the build server
3. Download nightly, rename to libgdx-x.y.z, upload to website /usr/share/nginx/html/releases
4. Setup pgp keys and settings.xml for Maven release, ask Mario
5. pull in the latest changes into your local libGDX repository
6. pull in the latest nightlies via `ant -f fetch.xml`
7. `mvn release:clean`, this should print `BUILD SUCCESSFUL`, if not, you've done something wrong
7. `mvn release:prepare -DdryRun=true` to test the prepare, see http://www.jroller.com/robertburrelldonkin/entry/apache_using_dry_run_with
7. `mvn release:prepare`, enter the release version number, then make sure the new snapshot version is x.y.z+1 or x.y+1.0 if you released an API breaking change
8. `mvn release:perform`, pray that everything works
9. Log into http://oss.sonatype.com (ask Mario for username:password) and do what's described at https://docs.sonatype.org/display/Repository/Sonatype+OSS+Maven+Repository+Usage+Guide#SonatypeOSSMavenRepositoryUsageGuide-8a.ReleaseIt