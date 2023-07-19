import org.jenkinsci.plugins.pipeline.modeldefinition.Utils
import org.jenkinsci.plugins.pipeline.modeldefinition.parser.PipelineModelTranslator
import org.jenkinsci.plugins.workflow.cps.CpsFlowDefinition
import org.jenkinsci.plugins.workflow.job.WorkflowJob
import org.jenkinsci.plugins.workflow.job.WorkflowRun
import org.junit.Before
import org.junit.Test
import static org.junit.Assert.assertEquals
import static org.junit.Assert.assertNotNull

pipeline {
  agent {
    node {
      label 'kaniko'
    }

  }
  stages {
    stage('Build and Publish DB') {
      steps {
        container(name: 'kaniko') {
          sh '''echo \'{ "credsStore": "ecr-login" }\' > /kaniko/.docker/config.json
/kaniko/executor -f `pwd`/compose/Dockerfile.db -c `pwd` --insecure --skip-tls-verify --cache=false --destination=${ECR_REPO}:${JOB_NAME}db-dev-${BUILD_NUMBER}'''
        }

      }
    }
    stage('Build and Publish API') {
      steps {
        container(name: 'kaniko') {
          sh '''echo \'{ "credsStore": "ecr-login" }\' > /kaniko/.docker/config.json
/kaniko/executor -f `pwd`/compose/Dockerfile.api -c `pwd` --insecure --skip-tls-verify --cache=false --destination=${ECR_REPO}:${JOB_NAME}api-dev-${BUILD_NUMBER}'''
        }
      }
    }
    stage('Build Trading Client') {
      steps {
        container(name: 'kaniko') {
          sh '''echo \'{ "credsStore": "ecr-login" }\' > /kaniko/.docker/config.json
/kaniko/executor -f `pwd`/autoclient/Dockerfile.autoclient -c `pwd` --insecure --skip-tls-verify --cache=false --destination=${ECR_REPO}:${JOB_NAME}ac-dev-${BUILD_NUMBER}'''
        }
      }
    }
  }
  environment {
    ECR_REPO = '108174090253.dkr.ecr.us-east-1.amazonaws.com/sre-course'
  }
  triggers {
    pollSCM('*/10 * * * 1-5')
  }
}



class JenkinsfileUnitTest {

    @Before
    void setUp() {
        // Set up any preconditions or configurations here if needed.
    }

    @Test
    void testBuildAndPublishDB_StageSuccess() {
        // Define the pipeline script for the 'Build and Publish DB' stage
        def pipelineScript = '''
        pipeline {
          agent any
          environment {
            ECR_REPO = 'your-ecr-repo-url'
          }
          stages {
            stage('Build and Publish DB') {
              steps {
                container(name: 'kaniko') {
                  sh 'echo \'{ "credsStore": "ecr-login" }\' > /kaniko/.docker/config.json'
                  // Simulate a successful build
                  // Your original build command is skipped here to keep the example simple
                }
              }
            }
          }
        }
        '''

        // Convert the pipeline script to a map representation
        def pipelineScriptAsMap = Utils.readYaml(pipelineScript)

        // Create a Jenkins pipeline instance for testing
        def pipeline = new PipelineModelTranslator().translate(pipelineScriptAsMap)
        assertNotNull("Pipeline instance should not be null", pipeline)

        // Create a test workflow job
        def job = new WorkflowJob('test-pipeline')
        job.setDefinition(new CpsFlowDefinition(pipeline.exportToCps()))

        // Simulate a pipeline run
        WorkflowRun run = job.scheduleBuild2(0).get()

        // Verify the build result for 'Build and Publish DB' stage
        assertEquals("Build should succeed", hudson.model.Result.SUCCESS, run.result)
    }

    @Test
    void testBuildAndPublishDB_StageFailure() {
        // Define the pipeline script for the 'Build and Publish DB' stage
        def pipelineScript = '''
        pipeline {
          agent any
          environment {
            ECR_REPO = 'your-ecr-repo-url'
          }
          stages {
            stage('Build and Publish DB') {
              steps {
                container(name: 'kaniko') {
                  sh 'echo \'{ "credsStore": "ecr-login" }\' > /kaniko/.docker/config.json'
                  // Simulate a failed build
                  // Your original build command is skipped here to keep the example simple
                  // Assume the build fails due to some issue
                  error "Build failed due to some error."
                }
              }
            }
          }
        }
        '''

        // Convert the pipeline script to a map representation
        def pipelineScriptAsMap = Utils.readYaml(pipelineScript)

        // Create a Jenkins pipeline instance for testing
        def pipeline = new PipelineModelTranslator().translate(pipelineScriptAsMap)
        assertNotNull("Pipeline instance should not be null", pipeline)

        // Create a test workflow job
        def job = new WorkflowJob('test-pipeline')
        job.setDefinition(new CpsFlowDefinition(pipeline.exportToCps()))

        // Simulate a pipeline run
        WorkflowRun run = job.scheduleBuild2(0).get()

        // Verify the build result for 'Build and Publish DB' stage
        assertEquals("Build should fail", hudson.model.Result.FAILURE, run.result)
    }
}

// // Unit Test

// import org.jenkinsci.plugins.pipeline.modeldefinition.Utils
// import org.jenkinsci.plugins.pipeline.modeldefinition.parser.PipelineModelTranslator
// import org.jenkinsci.plugins.workflow.cps.CpsFlowDefinition
// import org.jenkinsci.plugins.workflow.job.WorkflowJob
// import org.jenkinsci.plugins.workflow.job.WorkflowRun
// import org.jenkinsci.plugins.workflow.job.WorkflowJobProperty
// import org.jenkinsci.plugins.workflow.multibranch.BranchJobProperty
// import org.jenkinsci.plugins.workflow.multibranch.WorkflowMultiBranchProject
// import org.junit.Before
// import org.junit.Test
// import static org.junit.Assert.assertEquals
// import static org.junit.Assert.assertNotNull

// class JenkinsfileUnitTest {

//     @Before
//     void setUp() {
//         // Set up any preconditions or configurations here if needed.
//     }

//     @Test
//     void testBuildTradingClientStage() {
//         // Define the pipeline script
//         def pipelineScript = '''
//         pipeline {
//           agent any
//           environment {
//             ECR_REPO = 'your-ecr-repo-url'
//           }
//           stages {
//             stage('Build Trading Client') {
//               steps {
//                 container(name: 'kaniko') {
//                   sh 'echo \'{ "credsStore": "ecr-login" }\' > /kaniko/.docker/config.json'
//                   // Your original command is skipped here to keep the example simple
//                 }
//               }
//             }
//           }
//         }
//         '''

//         // Convert the pipeline script to a map representation
//         def pipelineScriptAsMap = Utils.readYaml(pipelineScript)

//         // Create a Jenkins pipeline instance for testing
//         def pipeline = new PipelineModelTranslator().translate(pipelineScriptAsMap)
//         assertNotNull("Pipeline instance should not be null", pipeline)

//         // Create a test workflow job
//         def job = new WorkflowJob('test-pipeline')
//         job.setDefinition(new CpsFlowDefinition(pipeline.exportToCps()))

//         // Simulate a pipeline run
//         WorkflowRun run = job.scheduleBuild2(0).get()

//         // Verify the build result
//         assertEquals("Build should succeed", hudson.model.Result.SUCCESS, run.result)
//         // Add more assertions as needed for the actual build behavior.
//     }
// }
