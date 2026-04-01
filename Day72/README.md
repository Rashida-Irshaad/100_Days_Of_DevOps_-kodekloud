# Day 72 - Challenge 
# ⚙️ Jenkins Parameterized Job - Basic Testing

## 📜 Task Description
A new DevOps Engineer needs to understand the basics of parameterized builds in Jenkins. The task is to create a simple parameterized job that accepts user inputs and prints them during execution.

---

## 🚀 Solution Steps

### 1️⃣ Access Jenkins
- Click on the **Jenkins** button in the top bar.
- Login using:
  - **Username:** admin  
  - **Password:** Adm!n321  

---

### 2️⃣ Create Jenkins Job
- Click **New Item**
- Enter job name: `parameterized-job`
- Select **Freestyle Project**
- Click **OK**

---

### 3️⃣ Configure Parameters

#### ✅ Enable Parameterization
- Check: **This project is parameterized**

---

#### 🔸 Add String Parameter
- Click **Add Parameter → String Parameter**
- Configure:
  - **Name:** Stage  
  - **Default Value:** Build  
  - **Description:** Build stage  

---

#### 🔸 Add Choice Parameter
- Click **Add Parameter → Choice Parameter**
- Configure:
  - **Name:** env  
  - **Choices:**
    ```
    Development
    Staging
    Production
    ```

---

### 4️⃣ Configure Build Step
- Scroll to **Build Steps**
- Click **Add build step → Execute shell**
- Add the following command:

```bash
echo "Stage is: $Stage"
echo "Environment is: $env"
5️⃣ Save the Job
Click Save
6️⃣ Build the Job
Click Build with Parameters
Set:
Stage: Build (default)
env: Production
Click Build
7️⃣ Verify Output
Open Console Output
Expected result:
Stage is: Build
Environment is: Production
Finished: SUCCESS