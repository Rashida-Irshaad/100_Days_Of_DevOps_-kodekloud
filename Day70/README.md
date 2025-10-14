# Day 70 - Challenge 
## 🧩 Jenkins User Access Configuration — Project-Based Matrix Authorization

The Nautilus DevOps team is integrating **Jenkins** into their CI/CD pipelines. After setting up a new Jenkins server, they now need to configure **user access** and permissions for the development team. Follow the detailed steps below to set up **Matrix-based security** for Jenkins in a clean and organized manner.

---

### 🚀 Steps to Configure User Access in Jenkins

1. **Access Jenkins UI**  
   - Open your Jenkins web interface in the browser.  
   - Log in using the administrator account credentials.

2. **Install Required Plugin (If Not Already Installed)**  
   - Go to **Manage Jenkins → Plugins → Available plugins tab**.  
   - Search for **Matrix Authorization Strategy Plugin**.  
   - Check the box next to it and click **Install without restart**.  
   - Once installation completes, select **Restart Jenkins when installation is complete and no jobs are running**.  
   - Wait for Jenkins to restart and reappear on the login page.

3. **Enable Project-Based Matrix Authorization**  
   - After logging back in, go to **Manage Jenkins → Security → Configure Global Security**.  
   - Under **Authorization**, select **Project-based Matrix Authorization Strategy**.  
   - This will allow you to configure permissions per user.

4. **Add a New User**  
   - Navigate to **Manage Jenkins → Security → Manage Users → Create User**.  
   - Fill in the details (Username, Password, Full name, Email address).  
   - Click **Create User** to save.  
   - Example:  
     - Username: `rose`  
     - Full Name: `Rose`  
   - (Note: You can skip password details if you’re configuring through LDAP or other authentication methods.)

5. **Assign Permissions**  
   - Return to **Manage Jenkins → Configure Global Security**.  
   - In the **Project-based Matrix Authorization Strategy** section:  
     - Add the user **rose** (type the username and click **Add**).  
     - Assign the following permissions:  
       - **Overall → Read** ✅  
     - Ensure **admin** retains all **Overall → Administer** permissions.  
     - Remove all permissions for **Anonymous** users.

6. **Job-Level Permissions (Optional but Recommended)**  
   - Open an existing Jenkins job.  
   - Click **Configure → Enable project-based security**.  
   - Add **rose** and grant **Read** permission only.  
   - This ensures that the user can view the job but cannot make changes or trigger builds.

7. **Save and Apply**  
   - Click **Save** or **Apply** to commit changes.  
   - Log out and test logging in as the `rose` user to verify restricted access.  
   - Rose should only see the job but not modify or trigger it.

---

### 🧠 Notes
- If any UI element is missing, make sure you installed the **Matrix Authorization Strategy Plugin** properly.  
- Avoid clicking **Finish** immediately after restarting Jenkins—wait until the login page reappears.  
- For documentation or submission, capture screenshots of your steps or record using [loom.com](https://www.loom.com/).  

---

✅ **Result:**  
You’ve successfully set up Jenkins user access control using the **Project-Based Matrix Authorization Strategy**.  
The `rose` user now has limited read-only permissions, while the `admin` user retains full administrative privileges.

