pipeline {
  agent {
    kubernetes {
        yaml """\
    apiVersion: v1
    kind: Pod
    metadata:
      labels:
        builder: promotion
    spec:
      serviceAccountName: jenkins-agent
      containers:
      - name: awscli
        image: amazon/aws-cli
        command:
        - cat
        tty: true
    """.stripIndent()
    }
  }
  parameters {
    string(name: 'TeamName', defaultValue: 'orderbook', description: 'Your team name in the form of cXXX-team??')
  }
  stages {
    stage('Promote API to Production') {
      steps {
          container(name: 'awscli') {
            script {
              buildNumber = Jenkins.instance.getItem("${TeamName}-sre-orderbook").getItem('steve').lastSuccessfulBuild.number
            }
            sh '''
            export AWS_DEFAULT_REGION=us-east-1
            imgNum=''' + buildNumber + '''
            aws ecr-public get-login-password >ecrpw
            APIMANIFEST=$(aws ecr batch-get-image --repository-name sre-course --image-ids imageTag=${imageAPIDevName}${imgNum} --query 'images[].imageManifest' --output text)
            # Check API
            if [ $(aws ecr describe-images --repository-name sre-course | grep "${imageAPIProdName}${imgNum}" | wc -l) -eq 0 ]
            then
              # We don't have prod so tag Dev to prod
              if ! aws ecr put-image --repository-name sre-course --image-tag ${imageAPIProdName}${imgNum} --image-manifest "$APIMANIFEST"
              then
                exitvalue=1
              fi
            fi
            case ${exitvalue} in
              0) echo "Update OK"
                 ;;
              1) echo "API failed to push to repository"
                 ;;
              2) echo "DB failed to push to repository"
                 ;;
              12) echo "API and DB failed to push to repository"
                 ;;
            esac
            exit ${exitvalue}
'''
          }
        }
      }
      stage('Promote Database to Production') {
        steps {
            container(name: 'awscli') {
              script {
                buildNumber = Jenkins.instance.getItem('sre-orderbook').getItem('steve').lastSuccessfulBuild.number
              }
              sh '''
              export AWS_DEFAULT_REGION=us-east-1
              imgNum=''' + buildNumber + '''
              aws ecr-public get-login-password >ecrpw
              DBMANIFEST=$(aws ecr batch-get-image --repository-name sre-course --image-ids imageTag=${imageDBDevName}${imgNum} --query 'images[].imageManifest' --output text)
              # Database image
              if [ $(aws ecr describe-images --repository-name sre-course | grep "${imageDBProdName}${imgNum}" | wc -l) -eq 0 ]
              then
                # We don't have prod so tag Dev to prod
                if ! aws ecr put-image --repository-name sre-course --image-tag ${imageDBProdName}${imgNum} --image-manifest "$DBMANIFEST"
                then
                  exitvalue="${exitvalue}2"
                fi
              fi
              case ${exitvalue} in
                0) echo "Update OK"
                   ;;
                1) echo "API failed to push to repository"
                   ;;
                2) echo "DB failed to push to repository"
                   ;;
                12) echo "API and DB failed to push to repository"
                   ;;
              esac
              exit ${exitvalue}
'''
            }
          }
        }
        stage('Promote AutoClient to Production') {
          steps {
              container(name: 'awscli') {
                script {
                  buildNumber = Jenkins.instance.getItem('sre-orderbook').getItem('steve').lastSuccessfulBuild.number
                }
                sh '''
                export AWS_DEFAULT_REGION=us-east-1
                imgNum=''' + buildNumber + '''
                aws ecr-public get-login-password >ecrpw
                ACMANIFEST=$(aws ecr batch-get-image --repository-name sre-course --image-ids imageTag=${imageACDevName}${imgNum} --query 'images[].imageManifest' --output text)
                # Autoclient image
                if [ $(aws ecr describe-images --repository-name sre-course | grep "${imageACProdName}${imgNum}" | wc -l) -eq 0 ]
                then
                  # We don't have prod so tag Dev to prod
                  if ! aws ecr put-image --repository-name sre-course --image-tag ${imageACProdName}${imgNum} --image-manifest "$ACMANIFEST"
                  then
                    exitvalue="${exitvalue}2"
                  fi
                fi
                case ${exitvalue} in
                  0) echo "Update OK"
                     ;;
                  1) echo "API failed to push to repository"
                     ;;
                  2) echo "DB failed to push to repository"
                     ;;
                  12) echo "API and DB failed to push to repository"
                     ;;
                esac
                exit ${exitvalue}
'''
        }
      }
    }
  }
  environment {
    ECR_REPO = '108174090253.dkr.ecr.us-east-1.amazonaws.com/sre-course'
    imageAPIDevName="${TeamName}api-dev-"
    imageAPIProdName="${TeamName}api-prod-"
    imageDBDevName="${TeamName}db-dev-"
    imageDBProdName="${TeamName}db-prod-"
    imageACDevName="${TeamName}ac-dev-"
    imageProdDevName="${TeamName}ac-prod-"
  }
  triggers {
    pollSCM('*/10 * * * 1-5')
  }
}
