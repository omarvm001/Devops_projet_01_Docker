# Student List Application POC

## Context
POZOS, a forward-thinking IT company in France, specializes in software development for high schools. To remain competitive and enhance operational efficiency, the innovation department is spearheading a transformative initiative. Their goal is to overhaul the current infrastructure, making it scalable, easily deployable, and highly automated.

## Challenge
The existing student list application, built on PHP and Flask, is hosted on a single server. This setup not only lacks scalability but also hinders high availability, making it difficult to deploy new releases without issues. The organization is experiencing delays and setbacks every time a new update is introduced, leading to frustration and inefficiencies.

## Objective
POZOS seeks to create a Proof of Concept (POC) that showcases the benefits of Docker technology in decoupling their application infrastructure. By leveraging Docker, we aim to enhance the application's scalability, reliability, and deployment automation, providing the agility that POZOS needs in its software operations.

## Proposed Solution
Our approach involves containerizing the student list application using Docker, which will allow us to:

- **Decouple Services**: Separate the PHP web server and Flask API into distinct containers, enabling independent scaling and management.
- **Automate Deployments**: Implement CI/CD pipelines to streamline the release process, reducing the likelihood of errors during deployment.
- **Enhance Scalability**: Easily replicate containers to handle increased loads, ensuring that the application can grow alongside user demand.
- **Improve High Availability**: Utilize Docker orchestration tools (like Docker Compose or Kubernetes) to ensure that the application remains accessible even during updates or outages.

## Outcome
This POC will demonstrate how Docker can revolutionize POZOS's infrastructure, showcasing improved deployment processes, enhanced performance, and greater resilience. By embracing this modern technology, POZOS will not only boost its operational agility but also position itself as a leader in educational software solutions.

## Summary
Our project aims to pave the way for a more efficient, automated, and scalable future for POZOS's software development efforts, empowering them to respond swiftly to the evolving needs of the high school education sector.

