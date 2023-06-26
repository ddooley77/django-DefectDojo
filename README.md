# RapidFort

[RapidFort](https://www.rapidfort.com/) is a solution for building secure, optimized Docker containers.
This fork of DefectDojo will use container images optimized by he RapidFort Build-Time product instead of the original images.
This public use case optimizes an entire platform (DefectDojo) instead of indvidual images often found in the [RapidFort Community](https://github.com/rapidfort/community-images).

### Usage:
Exactly the same as before.

Only the docker-compose files were updated to use the optimized images e.g.
```
>     image: "defectdojo/defectdojo-django:${DJANGO_VERSION:-latest}"
97,99c86
<     image: "defectdojo/defectdojo-django:latest-rfhardened"

```

### DefectDojo was chosen because:
* RapidFort would like to contribute to Open Source and OWASP.
* Dojo easy to deploy.
* Dojo is easy to test.
    * It already has integration tests and unit tests in the project
    * (This saves a couple hours creating a coverage script).
* A RapidFort Customer asked could we do this (amongst other platforms).
* An initial RapidFort scan showed that there could be large savings, especially in the main python defectdojo-django image.
* The Dojo team is already security savvy.  As an example, when it comes to container images they use in the platform, they go for the alpine variants.  Hopefully we can incorporate RapidFort into their tooling.

### Goals:
* Create a smaller, more secure version (less CVEs and smaller attack surface) of DefectDojo.
* Make it available for public consumption and gain feedback from end-users.
* Longer term if this has value, we could create a pipeline for any new DefectDojo version and hopefully partner with the OWASP team on this.

### Resuts:
TBD

### Detailed Steps on how this was done:
1. RapidFort stub all conatiners.
   ```
   xargs -0 -n 1 rfstub < <(tr \\n \\0 <image_list.txt)
   ```
2. Update docker-compose.yml and docker-compose.override.integration_tests.yml with rfstubbed versions.
3. Run with postgres/mysql options, click around the UI
4. Run existing test cases
   ```
   docker/setEnv.sh unit_tests
   ./dc-up.sh 
   ./dc-up.sh mysql-rabbitmq
   docker/setEnv.sh integration_tests
   ./dc-up.sh
   ./dc-up.sh mysql-rabbitmq
   ```
5. RapidFort optimize (rfharden) all containers
   ```
   xargs -0 -n 1 rfharden < <(tr \\n \\0 < image_list_stubbed.txt)
   ```
6. Update docker-compose.yml and docker-compose.override.integration_tests.yml with the RapidFort optimized versions (rfhardened).
7. Sanity Check - re-run postgres/mysql options, click around the UI, check it all looks good.



# DefectDojo

<table>
   <tr styl="margin: 0; position: absolute; top: 50%; -ms-transform: translateY(-50%); transform: translateY(-50%);">
     <th><a href="https://opensourcesecurityindex.io/" target="_blank" rel="noopener">
         <img style="width: 282px; height: 56px" src="https://opensourcesecurityindex.io/badge.svg"
           alt="Open Source Security Index - Fastest Growing Open Source Security Projects" width="282" height="56" />
       </a></th>
     <th>
       <p><a href="https://www.owasp.org/index.php/OWASP_DefectDojo_Project"><img
             src="https://img.shields.io/badge/owasp-flagship%20project-orange.svg" alt="OWASP Flagship"></a> <a
           href="https://github.com/DefectDojo/django-DefectDojo"><img
             src="https://img.shields.io/github/release/DefectDojo/django-DefectDojo.svg" alt="GitHub release"></a> <a
           href="https://www.youtube.com/channel/UCWw9qzqptiIvTqSqhOFuCuQ"><img
             src="https://img.shields.io/badge/youtube-subscribe-%23c4302b.svg" alt="YouTube Subscribe"></a> <img
           src="https://img.shields.io/twitter/follow/defectdojo.svg?style=social&amp;label=Follow" alt="Twitter Follow">
       </p>
       <p><a href="https://github.com/DefectDojo/django-DefectDojo/actions"><img
             src="https://github.com/DefectDojo/django-DefectDojo/actions/workflows/unit-tests.yml/badge.svg?branch=master"
             alt="Unit Tests"></a><a href="https://github.com/DefectDojo/django-DefectDojo/actions"><img
             src="https://github.com/DefectDojo/django-DefectDojo/actions/workflows/integration-tests.yml/badge.svg?branch=master"
             alt="Integration Tests"></a> <a href="https://bestpractices.coreinfrastructure.org/projects/2098"><img
             src="https://bestpractices.coreinfrastructure.org/projects/2098/badge" alt="CII Best Practices"></a></p>
     </th>
   </tr>
 </table>

![Screenshot of DefectDojo](https://raw.githubusercontent.com/DefectDojo/django-DefectDojo/dev/docs/static/images/screenshot1.png)

[DefectDojo](https://www.defectdojo.com/) is a security orchestration and
vulnerability management platform.
DefectDojo allows you to manage your application security program, maintain
product and application information, triage vulnerabilities and
push findings to systems like JIRA and Slack. DefectDojo enriches and
refines vulnerability data using a number of heuristic algorithms that
improve with the more you use the platform.

## Demo

Try out the demo server at [demo.defectdojo.org](https://demo.defectdojo.org)

Log in with `admin / 1Defectdojo@demo#appsec`. Please note that the demo is publicly accessible and regularly reset. Do not put sensitive data in the demo.

## Quick Start

```sh
git clone https://github.com/DefectDojo/django-DefectDojo
cd django-DefectDojo
# building
./dc-build.sh
# running (for other profiles besides postgres-redis look at https://github.com/DefectDojo/django-DefectDojo/blob/dev/readme-docs/DOCKER.md)
./dc-up.sh postgres-redis
# obtain admin credentials. the initializer can take up to 3 minutes to run
# use docker-compose logs -f initializer to track progress
docker-compose logs initializer | grep "Admin password:"
```

Navigate to <http://localhost:8080>.


## Documentation

- [Official Docs](https://documentation.defectdojo.com/) ([latest](https://documentation.defectdojo.com/) | [dev](https://documentation.defectdojo.com/dev))
- [REST APIs](https://documentation.defectdojo.com/integrations/api-v2-docs/)
- [Client APIs and Wrappers](https://documentation.defectdojo.com/integrations/api-v2-docs/#clients--api-wrappers)
- [Authentication Options](readme-docs/AVAILABLE-PLUGINS.md)
- [Parsers](https://documentation.defectdojo.com/integrations/parsers/)

## Supported Installation Options

* [Docker / Docker Compose](readme-docs/DOCKER.md)
* [SaaS](https://www.defectdojo.com/pricing) - Includes Support & Supports the Project
* [AWS AMI ](https://aws.amazon.com/marketplace/pp/prodview-m2a25gr67xbzk) - Supports the Project
* [godojo](https://github.com/DefectDojo/godojo)


## Community, Getting Involved, and Updates

[<img src="https://raw.githubusercontent.com/DefectDojo/django-DefectDojo/dev/docs/static/images/slack-logo-icon.png" alt="Slack" height="50"/>](https://owasp-slack.herokuapp.com/)
[<img src="https://raw.githubusercontent.com/DefectDojo/django-DefectDojo/dev/docs/static/images/Linkedin-logo-icon-png.png" alt="LinkedIn" height="50"/>](https://www.linkedin.com/company/defectdojo)
[<img src="https://raw.githubusercontent.com/DefectDojo/django-DefectDojo/dev/docs/static/images/Twitter_Logo.png" alt="Twitter" height="50"/>](https://twitter.com/defectdojo)
[<img src="https://raw.githubusercontent.com/DefectDojo/django-DefectDojo/dev/docs/static/images/YouTube-Emblem.png" alt="Youtube" height="50"/>](https://www.youtube.com/channel/UCWw9qzqptiIvTqSqhOFuCuQ)

[Join the slack community](https://owasp.org/slack/invite) and discussion! Realtime discussion is done in the OWASP Slack Channel, #defectdojo.
Follow DefectDojo on [Twitter](https://twitter.com/defectdojo), [Linkedin](https://www.linkedin.com/company/defectdojo), and [YouTube](https://www.youtube.com/channel/UCWw9qzqptiIvTqSqhOFuCuQ) for project updates!

## Contributing

:warning: Please note that DefectDojo will soon stop accepting new features to stabilize the API and data model for a
forthcoming v3 release. See the contributing guidelines below for more details. :warning:

See our [Contributing guidelines](readme-docs/CONTRIBUTING.md)

## Commercial Support and Training
[Commercial support and training is availaible.](https://www.defectdojo.com/) For information please email info@defectdojo.com.

## About Us

DefectDojo is maintained by:
* Greg Anderson ([@devGregA](https://github.com/devgrega) | [linkedin](https://www.linkedin.com/in/g-anderson/))
* Matt Tesauro ([@mtesauro](https://github.com/mtesauro) | [linkedin](https://www.linkedin.com/in/matttesauro/) | [@matt_tesauro](https://twitter.com/matt_tesauro))

Core Moderators can help you with pull requests or feedback on dev ideas:
* Cody Maffucci ([@Maffooch](https://github.com/maffooch) | [linkedin](https://www.linkedin.com/in/cody-maffucci))

Moderators can help you with pull requests or feedback on dev ideas:
* Damien Carol ([@damnielcarol](https://github.com/damiencarol) | [linkedin](https://www.linkedin.com/in/damien-carol/))
* Jannik Jürgens ([@alles-klar](https://github.com/alles-klar))
* Dubravko Sever ([@dsever](https://github.com/dsever))


## Hall of Fame
* Valentijn Scholten ([@valentijnscholten](https://github.com/valentijnscholten) | [sponsor](https://github.com/sponsors/valentijnscholten) | [linkedin](https://www.linkedin.com/in/valentijn-scholten/)) - Valentijn served as a core moderator for 3 years. Valentijn’s contributions were numerous and extensive. He overhauled, improved, and optimized many parts of the codebase. He consistently fielded questions, provided feedback on pull requests, and provided a helping hand wherever it was needed.
* Fred Blaise ([@madchap](https://github.com/madchap) | [linkedin](https://www.linkedin.com/in/fredblaise/)) - Fred served as a core moderator during a critical time for DefectDojo. He contributed code, helped the team stay organized, and architected important policies and procedures.
* Charles Neill ([@ccneill](https://twitter.com/ccneill)) – Charles served as a
    DefectDojo Maintainer for years and wrote some of Dojo's core functionality.
* Jay Paz ([@jjpaz](https://twitter.com/jjpaz)) – Jay was a DefectDojo
  maintainer for years. He performed Dojo's first UI overhaul, optimized code structure/features, and added numerous enhancements.


## Security

Please report Security issues via our [disclosure policy](readme-docs/SECURITY.md).

## License

DefectDojo is licensed under the [BSD-3-Clause License](LICENSE.md)
