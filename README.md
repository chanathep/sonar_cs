# sonar_cs

```
dotnet new console -o HelloWorld
```

.gitignore
```
bin/
Bin/
obj/
Obj/
```

sonar-project.properties
```
sonar.projectKey=<your project key>
sonar.sources=.
```

.github\worlflows\sonarqube.yml
```
on:
  # Trigger analysis when pushing to your main branches, and when creating a pull request.
  push:
    branches:
      - main
      - master
      - develop
      - 'releases/**'
  pull_request:
    types: [opened, synchronize, reopened]
name: SonarQube Scan
jobs:
  sonarqube:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          # Disabling shallow clones is recommended for improving the relevancy of reporting
          fetch-depth: 0
      - name: Setup dotnet
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: '5.0.x'
      - name: Install dependencies
        run: dotnet restore
      - name: SonarQube Scan
        uses: sonarsource/sonarqube-scan-action@master
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
          SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}
      - name: Build
        run: dotnet build
      - name: Test with the dotnet CLI
        run: dotnet test --settings coverlet.runsettings --logger:trx
        env:
          ASPNETCORE_ENVIRONMENT: Development
      - name: SonarQube end
        run: dotnet sonarscanner end /d:sonar.login="${{ secrets.SONAR_TOKEN }}"
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```