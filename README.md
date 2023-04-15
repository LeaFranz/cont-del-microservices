# Exercises for Continuous Delivery in agile Software Development

## Exercise 2: Microservices

Exercise 2  using [this](https://semaphoreci.com/community/tutorials/building-and-testing-a-rest-api-in-go-with-gorilla-mux-and-postgresql) tutorial.

Additional features:

- get most expensive products
- get cheapest products
- get products by name

## Exercise 3: Continuous Integration Workflow

Continuous Integration Workflow with GitHub Actions

[![Build and Test](https://github.com/LeaFranz/cont-del-microservices/actions/workflows/go.yml/badge.svg)](https://github.com/LeaFranz/cont-del-microservicesp/actions/workflows/ci.yml)

### GitHub Action: Build and test

Added a go.yml file with the default format for Go from Github and adapted it to use the PostgreSQL database.

#### Database specific code
```yml
    services:
      postgres:
        image: postgres
        env:
          POSTGRES_USER: postgres
          POSTGRES_PASSWORD: cont_del
          POSTGRES_DB: postgres
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          # Maps tcp port 5432 on service container to the host
          - 5432:5432
```

```yml
   - name: Test
      env:
        POSTGRES_HOST: postgres
        POSTGRES_PORT: ${{ job.services.postgres.ports['5432'] }}
        APP_DB_USERNAME: postgres
        APP_DB_PASSWORD: cont_del
        APP_DB_NAME: postgres
      run: go test -v ./...
```

### Static code analysis

Sonar cloud was integrated for the static code analysis.

```yml
  sonarcloud:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
      with:
        # Disabling shallow clone is recommended for improving relevancy of reporting
        fetch-depth: 0
    - name: SonarCloud Scan
      uses: sonarsource/sonarcloud-github-action@master
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
      with:
        args: >
          -Dsonar.projectKey=LeaFranz_cont-del-microservices
          -Dsonar.organization=leafranz
          -Dsonar.sources=.
          -Dsonar.host.url=https://sonarcloud.io
          -Dsonar.login=${{ secrets.SONAR_TOKEN }}
```