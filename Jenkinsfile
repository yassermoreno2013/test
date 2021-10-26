pipeline {
  agent any
    
 
    stages {

      
     stage('STAGE1') {
        
          steps {
              script  {
               echo "DEV"
			   powershell ''' 
			    cd "C:\\Program Files\\" ; dir
			   			   '''
			     
			   } 
             }
              
         }
         
       stage('STAGE2') {
        
         steps {
              script  {
                echo "TAG NUEVO 15"
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
