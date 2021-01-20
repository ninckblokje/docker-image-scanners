# Docker Image Scanners

- [Anchore](https://anchore.com/)
- [Clair](https://github.com/quay/clair)
- [Trivy](https://github.com/aquasecurity/trivy)

Both Anchore and Clair environments can be started using [my-dev-env](https://github.com/ninckblokje/my-dev-env), which provides Docker compose files for both.

Test images:
- `ninckblokje/helm:latest`
  - Contains only OS packages
  - No CVE's (2021-01-20)
- `registry.access.redhat.com/ubi7/ubi:7.6`
  - Contains only OS packages
  - Multiple CVE's on OS packages
- `ninckblokje/crasher:sh`
  - Contains OS packages and a Java applications
  - Multiple CVE's on OS packages
- `ninckblokje/ratetraining:1.1.0`
  - Contains OS packages and a Java Spring Boot application
  - Multiple CVE's on OS packages and JAR's
- `ninckblokje/polyglot-micronaut-express:1.0.0`
  - Contains OS packages, Java Micronaut application and a Node application
  - Multiple CVE's on OS packages, JAR's and Node modules

## Anchore

Note: It will take quite some time for Anchore to load the database, before images can be scanned!

### Commands

- System status: `docker-compose exec jnb-anchore-api anchore-cli system status`
- Feed list: `docker-compose exec jnb-anchore-api anchore-cli system feeds list`
- Refresh feeds: `docker-compose exec jnb-anchore-api anchore-cli system feeds sync`
- Add image: `docker-compose exec jnb-anchore-api anchore-cli image add [IMAGE_NAME]`
- Image vulnerabilities: `docker-compose exec jnb-anchore-api anchore-cli image vuln [IMAGE_NAME] all`

### Test images results

| Image | Result | OK / NOK |
|-------|--------|----------|
| `ninckblokje/helm:latest` | No CVE's (2021-01-20) | OK |
| `registry.access.redhat.com/ubi7/ubi:7.6` | Multiple CVE's on OS packages | OK |
| `ninckblokje/crasher:sh` | Multiple CVE's on OS packages | OK |
| `ninckblokje/ratetraining:1.1.0` | Multiple CVE's on OS packages and JAR's | OK |
| `ninckblokje/polyglot-micronaut-express:1.0.0` | Multiple CVE's on OS packages, JAR's and Node modules | OK |

## Clair

Note: Clair does not find most CVE's from the example images

### Commands

- Report image: `docker-compose exec jnb-clair clairctl report [IMAGE_NAME]`

### Test images results

| Image | Result | OK / NOK |
|-------|--------|----------|
| `ninckblokje/helm:latest` | No CVE's (2021-01-20) | OK |
| `registry.access.redhat.com/ubi7/ubi:7.6` | Multiple CVE's on OS packages | OK |
| `ninckblokje/crasher:sh` | No CVE's | NOK |
| `ninckblokje/ratetraining:1.1.0` | No CVE's | NOK |
| `ninckblokje/polyglot-micronaut-express:1.0.0` | Onle CVE's on OS packages | NOK |

## Trivy

Trivy runs as a command line tool.

### Commands:

- Scan image: `docker run --rm aquasec/trivy image --timeout 5ms0 [IMAGE_NAME]`
- Clear cache: `docker run --rm aquasec/trivy image --clear-cache`

### Test images results

| Image | Result | OK / NOK |
|-------|--------|----------|
| `ninckblokje/helm:latest` | Not supported | NOK |
| `registry.access.redhat.com/ubi7/ubi:7.6` | Multiple CVE's on OS packages | OK |
| `ninckblokje/crasher:sh` | Multiple CVE's on OS packages | OK |
| `ninckblokje/ratetraining:1.1.0` | Multiple CVE's on OS packages | NOK |
| `ninckblokje/polyglot-micronaut-express:1.0.0` | Onle CVE's on OS packages and Node modules | NOK |
