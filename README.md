// chat api
//controller

 public function userChat_post()
       {  
        $data=[
          'receiver_id'=>$this->input->post('receiver_id'),
          'message'=>$this->input->post('message')
         ];

         if(check_required($data)){
          return $this->response(['status'=>400,'message'=>'Missing parameter:'.check_required($data)],REST_Controller::HTTP_BAD_REQUEST);
         }
         $data['sender_id']=$this->id;
          $sender_id=$data['sender_id'];
          //echo $sender_id;die;
         $receiver_id=$this->input->post('receiver_id');
         $message=$this->input->post('message');

         $result=$this->ApiModel->check_id($sender_id,$receiver_id,$data,$message);
         if($result){
          $this->response(['status'=>200,'message'=>'User Chat','data'=>$result],REST_Controller::HTTP_OK);
        }
        else{
          $this->response(['status'=>200,'message'=>'User chat','data'=>$result],REST_Controller::HTTP_OK);
        } 

       }

//model

     public function check_id($sender_id,$receiver_id,$data,$message)
     { 
       // echo"$sender_id";die;
        $query=$this->db->query("select * from thread_table where (sender_id='$sender_id' AND receiver_id='$receiver_id') 
        OR (sender_id='$receiver_id' AND receiver_id='$sender_id')")->row_array();
         if(!$query)
         {    
          $this->db->insert('thread_table',$data);
         $query1 = $this->db->query("select * from thread_table where (sender_id='$sender_id' AND receiver_id='$receiver_id') 
          OR (sender_id='$receiver_id' AND receiver_id='$sender_id')")->row_array();
          $thread_id=$query1['id'];
         // echo $thread_id;die;
          $data['thread_id']=$thread_id;
          $this->db->insert('message_table',$data);
          $query2=$this->db->query("select * from thread_table where (sender_id='$sender_id' AND receiver_id='$receiver_id') 
          OR (sender_id='$receiver_id' AND receiver_id='$sender_id')")->row_array();
           return $query1;  
        }
        else
        {   
          $this->db->query("select * from thread_table where (sender_id='$sender_id' AND receiver_id='$receiver_id') 
          OR (sender_id='$receiver_id' AND receiver_id='$sender_id')")->row_array();
          $thread_id=$query['id'];
         // echo $thread_id;die;
          $data['thread_id']=$thread_id;
           $this->db->insert('message_table',$data);
           $this->db->query("update thread_table set sender_id='$sender_id',receiver_id='$receiver_id', message='$message' where (sender_id='$sender_id' AND 
            receiver_id='$receiver_id') OR (sender_id='$receiver_id' AND  receiver_id='$sender_id')");
            $query1=$this->db->query("select * from thread_table where (sender_id='$sender_id' AND receiver_id='$receiver_id') 
            OR (sender_id='$receiver_id' AND receiver_id='$sender_id')")->row_array();
             return $query1; 
             }
    }
