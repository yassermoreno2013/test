def remote = [:]
remote.name = "vservertopaz2"
remote.host = "52.116.192.113"
remote.allowAnyHosts = true


pipeline {
  agent any
    
 
    stages {

 
 stage('SCM') {
        steps
       {
        checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '054c1175-f6ca-4332-9f75-ebed5b9f6454', url: 'https://yassermoreno2013@bitbucket.org/yassermoreno2013/actualizaciones-topaz.git']]])
       }
    }
    
      
     stage('HACIENDO PULL EN HOST REMOTO') {
        
          steps {
              script  {
                withCredentials([sshUserPrivateKey(credentialsId: '1c88e6c4-f8a9-4b6d-afd7-81720009bf44', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')])
                  {
                  remote.user = userName
                  remote.identityFile = identity
                  sshCommand remote: remote, command: "cd /home/topaz/actualizaciones-topaz/ && git pull";
                  }
                } 
              }
              
         }
         
       stage('VERIFICA HM_STATUS') {
        
         steps {
              script  {
                withCredentials([sshUserPrivateKey(credentialsId: '1c88e6c4-f8a9-4b6d-afd7-81720009bf44', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')])
                  {
                  remote.user = userName
                  remote.identityFile = identity
                  sshCommand remote: remote, command: "cd /home/topaz/wf10 && sh /home/topaz/hakunamatata/bin/hm_execute/hm_status.sh ";
                  }
                } 
             }
        }   
         
         
        stage('CREANDO ARCHIVO CON NOMBRE ACTUALIZACION') { 
          steps {
              script  {
                  withCredentials([sshUserPrivateKey(credentialsId: '1c88e6c4-f8a9-4b6d-afd7-81720009bf44', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')])
                  {
                  remote.user = userName
                  remote.identityFile = identity
                  sshCommand remote: remote, command: "ls /home/topaz/actualizaciones-topaz/actualizaciones > /home/topaz/actualizaciones-topaz/nombreactualizacion.txt"
                
                  }
            } 
         }  
      }
        
       stage('VERIFICA SCRIPTS SQL A APLICAR - ACTUALIZA BD') {
            
            steps {
                script {
                
             /*    if (sh ("""[ -d "/home/flyway-7.7.0/sql" ] && echo "true" """ ).trim().endsWith("true"))
                              {
                              def CANTSQL = sh ( "ls /home/flyway-7.7.0/sql/*.sql | wc -w", failOnError: true).trim().toInteger()
                              
                              echo "Cantidad de archivos .SQL : '$CANTSQL'"
                               
                                if(CANTSQL > 0) {
                                  isCarpetaUpdated = true
                                  sh "rm /home/flyway-7.7.0/sql/*.sql"
                                                      } 
                                 else {
                                   echo "La carpeta 'sql' de FLYWAY, no contiene archivos para eliminar (archivos .sql)"
                                      }
                             
                              } 
                               else {
                                 echo "FLYWAY No contiene carpeta 'sql' por lo tanto no hay nada que eliminar"
                                
                               }*/
                             
                             
                     
                         
                   withCredentials([sshUserPrivateKey(credentialsId: '1c88e6c4-f8a9-4b6d-afd7-81720009bf44', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')])
                            {
                        remote.user = userName
                        remote.identityFile = identity
                        def NOMBREACTUALIZACION= sshCommand (remote: remote, command: "cat /home/topaz/actualizaciones-topaz/nombreactualizacion.txt")
                             
                             
                            
                           if ( sshCommand( remote: remote, command: """[ -d "/home/topaz/actualizaciones-topaz/actualizaciones/${NOMBREACTUALIZACION}/sql" ] && echo "true" """, failOnError: false).trim().endsWith("true") ) 
                              {
                              def cantScripts = sshCommand( remote: remote, command: "ls /home/topaz/actualizaciones-topaz/actualizaciones/${NOMBREACTUALIZACION}/sql/*.sql | wc -w", failOnError: true).trim().toInteger()
                              
                              echo "Cantidad de scripts : '$cantScripts'"
                               
                                if(cantScripts > 0) {
                                  isDatabaseUpdated = true
                                    sshGet remote: remote, from: "/home/topaz/actualizaciones-topaz/actualizaciones/${NOMBREACTUALIZACION}/sql/*.sql", into: "/home/flyway-7.7.0/sql/", override: true
                           
                        
                        sh "ls /home/flyway-7.7.0/sql/*.sql"
                        
                        
                    
                    flywayrunner (
                        installationName: 'FLYWAY', 
                        flywayCommand: 'migrate', 
                        credentialsId: 'b5b9f88f-17a4-416c-bfda-cf06c00f92bf',  
                        url: "jdbc:oracle:thin:@52.117.22.234:1521:xe", 
                        locations:'filesystem:/home/flyway-7.7.0/sql/',
                        commandLineArgs: '-validateOnMigrate=false' 
                      
                    )
                      
                      
                                } 
                                 else {
                                   echo "La carpeta 'sql' de la actualizacion, no contiene scripts (archivos .sql)"
                                   echo "${NOMBREACTUALIZACION}"
                                      }
                              } 
                               else {
                                 echo "La actualizacion no contiene carpeta 'sql' por lo tanto no se actualiza la base de datos"
                                 echo "${NOMBREACTUALIZACION}" 
                               }
                      
                    
            }
        }
    }
}    
     
        
     stage('ACTUALIZA TOPAZ') {
        
          steps {
              script  {
                withCredentials([sshUserPrivateKey(credentialsId: '1c88e6c4-f8a9-4b6d-afd7-81720009bf44', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')])
                  {
                  remote.user = userName
                  remote.identityFile = identity
                  
                   sshCommand remote: remote, command: "cd /home/topaz/wf10 && sh /home/topaz/hakunamatata/bin/hm_execute/hm_actualizar.sh ALL -d"
                   
                  
                                 }
                            }
                       }                        
                  
                }
              
        stage('RE-VERIFICA HM_STATUS') {
        
          steps {
              script  {
                withCredentials([sshUserPrivateKey(credentialsId: '1c88e6c4-f8a9-4b6d-afd7-81720009bf44', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')])
                  {
                  remote.user = userName
                  remote.identityFile = identity
                  sshCommand remote: remote, command: "cd /home/topaz/wf10 && sh /home/topaz/hakunamatata/bin/hm_execute/hm_status.sh ";
                  }
                } 
             }
        } 
       
   stage('MUEVE ACTUALIZACION APLICADA') {
             steps {
                script {
                      withCredentials([sshUserPrivateKey(credentialsId: '1c88e6c4-f8a9-4b6d-afd7-81720009bf44', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')])
                  {
                  remote.user = userName
                  remote.identityFile = identity
                  def NOMBREACTUALIZACION= sshCommand (remote: remote, command: "cat /home/topaz/actualizaciones-topaz/nombreactualizacion.txt")
                  sshCommand remote: remote, command: "mv /home/topaz/actualizaciones-topaz/actualizaciones/${NOMBREACTUALIZACION} /home/topaz/actualizaciones-topaz/actualizaciones-aplicadas/${NOMBREACTUALIZACION}";
                  }
                    }
                  } 
             }
    
   /* stage('ELIMINANDO IMAGENES ANTERIORES EN DOCKER') {
        
          steps {
              script  {
                withCredentials([sshUserPrivateKey(credentialsId: '1c88e6c4-f8a9-4b6d-afd7-81720009bf44', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')])
                  {
                  remote.user = userName
                  remote.identityFile = identity
                  sshCommand remote: remote, command: "docker rmi topaz_wf10:v1 && docker rmi us.icr.io/topaznamespace/topaz_wf10:v1";
                   }
                } 
              }
        }*/ 
        
    stage('HACIENDO BUILD DE NUEVA IMAGEN(LUEGO DE ACTUALIZAR)') {
        
          steps {
              script  {
                withCredentials([sshUserPrivateKey(credentialsId: '1c88e6c4-f8a9-4b6d-afd7-81720009bf44', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')])
                  {
                  remote.user = userName
                  remote.identityFile = identity
                  def TAG = sshCommand remote: remote, command: "date +'%Y%m%d%H%M%S'";
                  sshCommand remote: remote, command: "cd /home/topaz/wf10 && docker build --tag=topaz_wf10:${TAG} .";
                  sshCommand remote: remote, command: "cd /home/topaz/wf10 &&  docker tag topaz_wf10:${TAG}  us.icr.io/topaznamespace/topaz_wf10:${TAG}";
                  sshCommand remote: remote, command: "ibmcloud login --apikey 0i_6cNaEn98-5l1ukjSkY1chcCk7nWXbJOeRrkW9IoL_"; 
                  sshCommand remote: remote, command: "ibmcloud config --check-version=false"
                  sshCommand remote: remote, command: "ibmcloud cr login";
                  sshCommand remote: remote, command: "cd /home/topaz/wf10  && docker push us.icr.io/topaznamespace/topaz_wf10:${TAG}";
                  sshCommand remote: remote, command: "ibmcloud oc cluster config -c topaz-cluster-mvp1 --admin";
                  sshCommand remote: remote, command: "oc set image deployment/topaz-transacciones topaz-transacciones=us.icr.io/topaznamespace/topaz_wf10:${TAG}";
                  sshCommand remote: remote, command: "oc set image deployment/topaz-consultas topaz-consultas=us.icr.io/topaznamespace/topaz_wf10:${TAG}";
                  
                  }
                } 
              }
        } 
          
      /*  stage('HACIENDO COPIA LOCAL DE IMAGEN PARA SUBIR AL CR') {
        
          steps {
              script  {
                withCredentials([sshUserPrivateKey(credentialsId: '1c88e6c4-f8a9-4b6d-afd7-81720009bf44', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')])
                  {
                  remote.user = userName
                  remote.identityFile = identity
                  sshCommand remote: remote, command: "cd /home/topaz/wf10 &&  docker tag topaz_wf10:${TAG}  us.icr.io/topaznamespace/topaz_wf10:${TAG}";
                  }
                } 
              }
        }*/ 
   
  
   /*stage('LOGGING A IBM Cloud') {
            steps {
                script {
                     withCredentials([sshUserPrivateKey(credentialsId: '1c88e6c4-f8a9-4b6d-afd7-81720009bf44', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')])
                      {
                      remote.user = userName
                      remote.identityFile = identity
                     
                    sshCommand remote: remote, command: "ibmcloud login --apikey 0i_6cNaEn98-5l1ukjSkY1chcCk7nWXbJOeRrkW9IoL_"; 
                }
            }
        }
   
   }*/
  
 /*  stage('ELIMINANDO IMAGEN ANTERIOR DEL CONTAINER IMAGE') {
        
          steps {
              script  {
                withCredentials([sshUserPrivateKey(credentialsId: '1c88e6c4-f8a9-4b6d-afd7-81720009bf44', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')])
                  {
                  remote.user = userName
                  remote.identityFile = identity
                       
                       sshCommand remote: remote, command: "ibmcloud cr login && ibmcloud cr image-rm us.icr.io/topaznamespace/topaz_wf10:v1"; 
                      
                   }
                } 
              }
        }*/   
     
      
       /* stage('SUBIENDO IMAGEN TAGEADA EN EL CR') {
        
          steps {
              script  {
                withCredentials([sshUserPrivateKey(credentialsId: '1c88e6c4-f8a9-4b6d-afd7-81720009bf44', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')])
                  {
                  remote.user = userName
                  remote.identityFile = identity
                  
                  sshCommand remote: remote, command: "cd /home/topaz/wf10  && docker push us.icr.io/topaznamespace/topaz_wf10:${TAG}";
                  }
                } 
              }
        } */     
           
         stage('LISTANDO IMAGENES EN EL CR') {
        
          steps {
              script  {
                withCredentials([sshUserPrivateKey(credentialsId: '1c88e6c4-f8a9-4b6d-afd7-81720009bf44', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')])
                  {
                  remote.user = userName
                  remote.identityFile = identity
                  sshCommand remote: remote, command: "cd /home/topaz/wf10 && ibmcloud cr image-list";
                  }
                } 
              }
              
         }
         
        
         
     /*    stage('ACTUALIZANDO  YAML REPO LOCAL') {
        
          steps {
              script  {
                
                powershell '''
                $lines = get-content D:\\SCHEMATICS_IBMCLOUD\\schematicsrepotopaz1\\yamls\\prueba.yaml
                $lines | foreach-object {$_ -replace "vers1", "vers2"} | set-content D:\\SCHEMATICS_IBMCLOUD\\schematicsrepotopaz1\\yamls\\prueba.yaml
                '''ibmcloud oc cluster config -c topaz-cluster-mvp1 --admin
            // $lines | foreach-object {$_ -replace "vers1", $env:TAG} | set-content D:\\SCHEMATICS_IBMCLOUD\\schematicsrepo\\yamls\\deploy-job.yaml
                  }
                } 
              }
              
        stage('ACTUALIZANDO YAML EN REPO GIT') { 
            
            steps {
                script  {
                   withCredentials([sshUserPrivateKey(credentialsId: '1c88e6c4-f8a9-4b6d-afd7-81720009bf44', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')])
                     {
                  remote.user = userName
                  remote.identityFile = identity
                  sshCommand remote: remote, command: '''cd  /home/topaz/schematicsrepotopaz1/yamls/ && git add . && git commit -m "Actualizando repositorio" && git push'''
                     }
            }
          }
        } */
       } 
         
         
   }