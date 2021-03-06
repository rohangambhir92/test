pipeline
{
    agent any
	//tools
	//{
	//	maven 'Maven'
	//}
	options
    {
        timeout(time: 1, unit: 'HOURS')
		
        // Discard old builds after 5 days or 5 builds count.
        buildDiscarder(logRotator(daysToKeepStr: '5', numToKeepStr: '5'))
	  
	    //To avoid concurrent builds to avoid multiple checkouts
	    disableConcurrentBuilds()
    }
    stages
    {
	    stage ('checkout')
		{
			steps
			{
				checkout scm
			}
		}
		stage ('Build')
		{
			steps
			{
				bat "mvn install"
			}
		}
		stage ('Unit Testing')
		{
			steps
			{
				bat "mvn test"
			}
		}
		stage ('Sonar Analysis')
		{
			steps
			{
				withSonarQubeEnv("Test_Sonar_DevOps") 
				{
					bat "mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.2:sonar"
				}
			}
		}
	    	stage ('Sonar Quality Gate Check')
	    	{
			steps
			{
				timeout(time: 1, unit: 'MINUTES') 
				{
                			waitForQualityGate abortPipeline: true
              			}
			}
		}
		stage ('Upload to Artifactory')
		{
			steps
			{
				rtMavenDeployer (
                    id: 'deployer',
                    serverId: '123456789@artifactory',
                    releaseRepo: 'rohangambhir_nagp_home_assignment',
                    snapshotRepo: 'rohangambhir_nagp_home_assignment'
                )
                rtMavenRun (
                    pom: 'pom.xml',
                    goals: 'clean install',
                    deployerId: 'deployer',
                )
                rtPublishBuildInfo (
                    serverId: '123456789@artifactory',
                )
			}
		}
	}
	post 
	{
        always 
		{
			echo "*********** Executing post tasks like Email notifications *****************"
        }
    }
}
