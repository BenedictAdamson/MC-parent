// Jenkinsfile for the MC project

/* 
 * © Copyright Benedict Adamson 2018.
 * 
 * This file is part of MC.
 *
 * MC is free software: you can redistribute it and/or modify
 * it under the terms of the GNU General Public License as published by
 * the Free Software Foundation, either version 3 of the License, or
 * (at your option) any later version.
 *
 * MC is distributed in the hope that it will be useful,
 * but WITHOUT ANY WARRANTY; without even the implied warranty of
 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 * GNU General Public License for more details.
 *
 * You should have received a copy of the GNU General Public License
 * along with MC.  If not, see <https://www.gnu.org/licenses/>.
 */
 
 /*
  * Jenkins plugins used:
  * Config File Provider
  *     - Should configure the file settings.xml with ID 'maven-settings' as the Maven settings file
  *     - That settings.cml configuration should provide authentication credentials
  *       (in server/servers elements) for the services with the following IDs:
  *         - MC.repo: the Maven release repository, at localhost:8081 
  *         - MC-SNAPSHOT.repo: the Maven SNAPSHOT repository, at localhost:8081 
  * Warnings 5+
  */
 
pipeline { 
    agent {
        dockerfile {
            filename 'Jenkins.Dockerfile'
            args '-v $HOME/.m2:/root/.m2 --network="host"'
        }
    }
    triggers {
        pollSCM('H */4 * * *')
    }
    stages {
        stage('Build') { 
            steps {
                configFileProvider([configFile(fileId: 'maven-settings', variable: 'MAVEN_SETTINGS')]){ 
                    sh 'mvn -s $MAVEN_SETTINGS clean package'
                }
            }
        }
        stage('Deploy') {
            when {
                branch 'master';
            }
            steps {
                configFileProvider([configFile(fileId: 'maven-settings', variable: 'MAVEN_SETTINGS')]){ 
                    sh 'mvn -s $MAVEN_SETTINGS deploy'
                }
            }
        }
    }
    post {
        success {
            archiveArtifacts artifacts: 'pom.xml', fingerprint: true
        }
    }
}