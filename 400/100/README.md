# 100 - Method 1: Resetting the admin password if Portainer runs as a container

You would typically use this method if you run the Portainer Server on Docker Standalone.

First, go to our [reset password container helper](https://github.com/portainer/helper-reset-password) in GitHub, then stop the Portainer container by running this command:
```
docker stop "id-portainer-container"
```
Next, run the helper using the following command (you'll need to mount the Portainer data volume):
```
docker pull portainer/helper-reset-password
docker run --rm -v portainer_data:/data portainer/helper-reset-password
```
If successful, the output should look like this:
```
2020/06/04 00:13:58 Password successfully updated for user: admin
2020/06/04 00:13:58 Use the following password to login: &_4#\3^5V8vLTd)E"NWiJBs26G*9HPl1
```
If the helper is unable to find an admin user to update, it will create a new one for you. If the username admin is already used, it will create a user named admin-[randomstring]:
```
2022/08/10 07:36:33 [WARN] Unable to retrieve user with ID 1, will try to create, err: object not found inside the database
2022/08/10 07:36:33 Admin user admin-u0512b3f0v4dqk7o successfully created
2022/08/10 07:36:33 Use the following password to login: Sr#]YL_6D0k8Pd{pA9^|}F32j5J4I=av
```
Finally, use this command to start the Portainer container then try logging in with the new password:
```
docker start "id-portainer-container"
```
