#!/usr/bin/env groovy
 // Liquibase declarative pipeline

 pipeline {
 agent any
 parameters {
        choice(name: 'step', choices: ['Test', 'Prod'], description: 'Select the Environment')
    
        choice(name: 'pipeline', choices: ['Test', 'Prod'], description: 'Select the Branch')
    }
 
 environment {
   PROJ="28Oct21_test"
   GITURL="https://github.com/mayank1dba"
   ENVIRONMENT_STEP="${params.step}"
   BRANCH="${params.pipeline}"
   PATH="/usr/apps/Liquibase-4.4.3-bin:$PATH"
   MysqlDB_CREDS=credentials('94a4216b-45f0-4ab6-b6e8-def33e6fde0a')
 
 
 }
 stages {

   stage ('Precheck') {
             steps {
                     sh '''
       { set +x; } 2>/dev/null
       echo "Current project: "$PROJ
       echo "Current scm branch: "$BRANCH
       echo "Current environment: "$ENVIRONMENT_STEP
                     '''
             } // steps
     } // stage 'precheck'

      stage ('Checkout on a git branch') {
      steps {
        // checkout Liquibase project from the git repository
        sh '''
         { set +x; } 2>/dev/null
         cd /usr/apps/Liquibase-4.4.3-bin/examples/${ENVIRONMENT_STEP}
         if [ -d "$PROJ" ]; then rm -Rf $PROJ; fi
         git clone ${GITURL}/${PROJ}.git
         cd ${PROJ}
         git checkout -b $BRANCH
         git status
         '''
     } // steps for checkout stages
   } // stage 'checkout'

          
  stage ('liquibase commands'){
   steps {
   sh '''
    { set +x; } 2>/dev/null
    cd /usr/apps/Liquibase-4.4.3-bin/examples/${ENVIRONMENT_STEP}/${PROJ}
     liquibase --version
     echo "------------------------------------"
     echo "----------liquibase status----------"
     echo "------------------------------------"
     liquibase --contexts=${ENVIRONMENT_STEP} --url=jdbc:mysql://192.168.47.144:3306/Test_DB_Liquibase --username=${MysqlDB_CREDS_USR} --password=${MysqlDB_CREDS_PSW} --changeLogFile=changelog.mysql.sql --driver=com.mysql.cj.jdbc.Driver status
     echo "---------------------------------------"
     echo "----------liquibase updateSQL----------"
     echo "---------------------------------------"
     liquibase --contexts=${ENVIRONMENT_STEP} --url=jdbc:mysql://192.168.47.144:3306/Test_DB_Liquibase --username=${MysqlDB_CREDS_USR} --password=${MysqlDB_CREDS_PSW} --changeLogFile=changelog.mysql.sql --driver=com.mysql.cj.jdbc.Driver updatesql
     echo "------------------------------------"
     echo "----------liquibase update----------"
     echo "------------------------------------"
     liquibase --contexts=${ENVIRONMENT_STEP} --url=jdbc:mysql://192.168.47.144:3306/Test_DB_Liquibase --username=${MysqlDB_CREDS_USR} --password=${MysqlDB_CREDS_PSW} --changeLogFile=changelog.mysql.sql --driver=com.mysql.cj.jdbc.Driver update
       '''
     } // steps
   }   // Liquibase commands stage

 //      stage ('Cleanup'){
 //         steps {
 //           sh '''
 //             { set +x; } 2>/dev/null
 //             echo "Deleting project workspace..."
 //             cd /localPath/to/workspaceFolder && rm -r ${PROJ}
 //           '''
 //         } // steps
 //       }   // Deleting project workspace
 } // stages
}  // pipeline
