pipeline {
  agent any
    
 
    stages {

      
     stage('STAGE1') {
        
          steps {
              script  {
               echo "DEV"
			   powershell ''' 
			    cd "C:\\Program Files\\Amazon\\AWSCLI\\bin\\" ; dir
			   			   '''
			     
			   } 
             }
              
         }
         
       stage('STAGE2') {
        
         steps {
              script  {
                echo "TAG NUEVO 13"
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