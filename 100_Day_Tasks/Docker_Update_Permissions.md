One of the Nautilus project developers need access to run docker commands on App Server 3. This user is already created on the server. Accomplish this task as per details given below:  
User john is not able to run docker commands on App Server 3 in Stratos DC, make the required changes so that this user can run docker commands without sudo.  

Solution:  
```bash
ssh banner@stapp03
sudo usermod -aG docker john
sudo systemctl restart docker
su - anita
docker ps -a
```
<img width="1393" height="628" alt="image" src="https://github.com/user-attachments/assets/93e09927-ccb6-4a29-ab09-4c0211798c1c" />
   
