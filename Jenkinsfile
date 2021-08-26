pipeline {
  agent any
    
 
    stages {

      
     stage('STAGE1') {
        
          steps {
              script  {
               echo "DEV"
			   powershell ''' 
			    cd C:\\Program Files\\Amazon\\AWSCLI\\bin\\
			   			   '''
			   powershell ''' 
			    dir
			   			   '''
			   

			   } 
              }
              
         }
         
       stage('STAGE2') {
        
         steps {
              script  {
                echo "TEST"
                } 
             }
        }   
         
         
        stage('STAGE3') { 
          steps {
              script  {
                  echo "BUILD"
            } 
         }  
      }
        
      
       } 
         
         
   }