pipeline{
    agent any
    parameters {
        choice(name: 'DO_SONNAR',choices:'yes\nno', description: '是否允许Sonar-Scanner?')
        string(name: 'GIT_BRANCH', defaultValue: 'dev', description: '请输入需要构建的分支')
    }
    stages {
        stage('代码构建'){
            parallel {
                stage('SonarQube扫描') {
                    when {
                        expression { DO_SONNAR == 'yes' }
                        //environment name : 'env.SONNAR', value : 'yes'
                    }
                    steps {
                        script {
                            sonar_home = tool 'sonar_scanner'
                        }
                        withSonarQubeEnv('sonar_service') {
                            sh '${sonar_home}/bin/sonar-scanner -Dsonar.projectKey=${JOB_NAME%%/*} -Dsonar.sources=./src'
                        }
                    }
                }
                stage('maven编译'){
                    steps{
                        //mvn构建
                        //sh "mvn clean install"
                        echo '打包'
                    }
                }
            }
        }
        stage("选择：构建环境") {
            steps{
                script {
                    timeout(time:1,unit:'HOURS'){
                        IS_DEPLOY = input message: "构建配置",
                        ok: "搞起",
                        parameters: [
                            [name:'DEPLOY_TEST',$class: 'BooleanParameterDefinition', defaultValue:false, description:'是否部署测试环境'],
                            [name:'DEPLOY_ONLINE',$class: 'BooleanParameterDefinition', defaultValue:false, description:'是否部署生产环境']
                            //choice(name: 'TEST', choices: 'yes\nno' ,description: '是否部署测试环境' ,defaultChoice: 'no'),
                            //choice(name: 'ONLINE', choices: 'yes\nno',description: '是否部署生产环境' ,defaultChoice: 'no')
                        ]
                    }
                }
                echo '选择构建环境'
            }
        }
        
        stage("部署测试环境"){
            when {
                expression { IS_DEPLOY["DEPLOY_TEST"] == true }
            }
            steps {
                echo "部署测试环境中……"
            }
        }
        
        stage("部署生产环境"){
            when {
                expression { IS_DEPLOY["DEPLOY_ONLINE"] == true }
            }
            steps {
                echo "部署生产环境中……"
            }
        }
    }
}
