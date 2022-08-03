pipeline {

    agent any

    parameters {
        gitParameter name: 'VERSAO',
        type: 'PT_BRANCH_TAG',
        defaultValue: 'dev'
    }
    environment {
        APP = "nome_do_app"
        REGISTRY = "registry.url.com"
        GIT_URL = ""
        DOCKER_SERVER = "10.21.0.126"
        DB_SERVER = ""
    }
    
    stages{
        stage('SCM'){
            steps{
                checkout ([
                    $class: 'GitSCM',
                    branches: [[ name: "${params.VERSAO}" ]],
                    doGenerateSubmoduleConfiguration: false,
                    extensions: [],
                    gitTool: 'Default',
                    submoduleCfg: [],
                    userRemoteConfigs: [[ credentialsId: 'usr_git_jenkins', url: "${env.GIT_URL}" ]]
                    ])
            }
        }

        stage('Docker Build'){
            steps{
                //===Reajuste de variável dentro de arquivo .env para apontamento do banco correto===\\
                //sh 'sed "s/<VARIAVEL>=.*/<DB_SERVER>/g" PATH/PARA/.env'

                //===TESTE PARA VERIFICAÇÃO DE VARIÁVEIS===\\
                echo "docker build -t ${env.REGISTRY}/${env.APP}:${params.VERSAO}"

                //===Build de imagem docker===\\
                //sh "docker build -t ${env.REGISTRY}/${env.APP}:${params.VERSAO} ."
            }
        }

        stage('Push to Registry'){
            steps{
                //===Docker login para efetuar o push===\\
                withCredentials([string(credentialsId: 'usr_registry', variable: 'DOCKERPWD')]) {
                    sh "docker login -u <NOME_DO_USUARIO> -p ${DOCKERPWD}"
                }
                //===TESTE PARA VERIFICAÇÃO DE VARIÁVEIS===\\
                echo "docker push ${env.REGISTRY}/${env.APP}:${params.VERSAO}"

                //===Push de imagem para o registry===\\
                //sh "docker push ${env.REGISTRY}/${env.APP}:${params.VERSAO}"
            }
        }
        
        /*stage('Aprovação em PRD'){
            steps {
                mail from: "<remetente>@dominio.com.br",
            	subject: "[Jenkins] Aguardado aprovação do JOB ${JOB_NAME} (${BUILD_NUMBER}) em produção.",
            	to: "<destinatário>@dominio.com.br",
            	body: "Favor acessar o link à seguir para aprovação: ${RUN_DISPLAY_URL} "
                
                timeout(time: 8, unit: 'HOURS') {
                input(id: 'prd-approve', message: 'Aprovar a publicação em Produção?', ok: 'Sim', submitter: 'usuario.fulano,usuario.ciclano')
                }
            }
        }

        stage('Kubernetes Deployment') {
            steps{
                sh 'kubectl apply -f PATH/TO/deployment.yaml'
            }
        }*/

        stage('Docker Run'){
            steps{
                //===Execuçao de conteiner com imagem recém-criada===\\
                sh "docker --host ssh://jenkins@${env.DOCKER_SERVER} run ${env.REGISTRY}/${env.APP}:${params.VERSAO} -e <VARIAVEL>=<DB_SERVER>"
            }
        }
    }
}
