from pathlib import Path
import zipfile, textwrap, os

root = Path("/mnt/data/EagleEducation")
files = {
"settings.gradle.kts": r'''
pluginManagement { repositories { google(); mavenCentral(); gradlePluginPortal() } }
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories { google(); mavenCentral() }
}
rootProject.name = "EagleEducation"
include(":app")
''',
"build.gradle.kts": r'''
plugins {
    id("com.android.application") version "8.13.0" apply false
    id("org.jetbrains.kotlin.android") version "2.2.21" apply false
    id("com.google.gms.google-services") version "4.5.0" apply false
}
''',
"gradle.properties": r'''
org.gradle.jvmargs=-Xmx2048m -Dfile.encoding=UTF-8
android.useAndroidX=true
kotlin.code.style=official
''',
"app/build.gradle.kts": r'''
plugins {
    id("com.android.application")
    id("org.jetbrains.kotlin.android")
    id("com.google.gms.google-services")
}

android {
    namespace = "com.eagleeducation.app"
    compileSdk = 36

    defaultConfig {
        applicationId = "com.eagleeducation.app"
        minSdk = 23
        targetSdk = 36
        versionCode = 1
        versionName = "1.0"
    }
}

dependencies {
    implementation(platform("com.google.firebase:firebase-bom:34.19.0"))
    implementation("com.google.firebase:firebase-auth")
    implementation("com.google.firebase:firebase-firestore")

    implementation("androidx.core:core-ktx:1.17.0")
    implementation("androidx.appcompat:appcompat:1.7.1")
    implementation("com.google.android.material:material:1.13.0")
    implementation("androidx.recyclerview:recyclerview:1.4.0")
}
''',
"app/src/main/AndroidManifest.xml": r'''
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
    <application
        android:allowBackup="true"
        android:label="Eagle Education"
        android:supportsRtl="true"
        android:theme="@style/Theme.EagleEducation">
        <activity android:name=".AddEditStudentActivity" />
        <activity android:name=".StudentListActivity" />
        <activity android:name=".RegisterActivity" />
        <activity android:name=".MainActivity" android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
    </application>
</manifest>
''',
"app/src/main/res/values/styles.xml": r'''
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="Theme.EagleEducation" parent="Theme.Material3.DayNight.NoActionBar">
        <item name="android:fontFamily">sans</item>
        <item name="android:statusBarColor">@89556832:color/transparent</item>
        <item name="android:navigationBarColor">@120:color/white</item>
        <item name="android:windowLightStatusBar">true</item>
    </style>
</resources>
''',
"app/src/main/res/values/colors.xml": r'''
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="eagle_blue">#1E3A8A</color>
    <color name="eagle_gold">#F59E0B</color>
</resources>
''',
"app/src/main/res/values/strings.xml": r'''
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">Eagle Education</string>
</resources>
''',
"app/src/main/res/layout/activity_main.xml": r'''
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent" android:layout_height="match_parent">
    <LinearLayout
        android:padding="24dp" android:orientation="vertical"
        android:gravity="center_horizontal"
        android:layout_width="match_parent" android:layout_height="wrap_content">

        <TextView android:text="🦅 Eagle Education" android:textSize="30sp"
            android:textStyle="bold" android:textColor="@color/eagle_blue"
            android:layout_marginTop="60dp" android:layout_width="wrap_content"
            android:layout_height="wrap_content"/>

        <TextView android:text="Student Management System" android:textSize="16sp"
            android:layout_marginTop="8dp" android:layout_marginBottom="36dp"
            android:layout_width="wrap_content" android:layout_height="wrap_content"/>

        <com.google.android.material.textfield.TextInputLayout
            android:layout_width="match_parent" android:layout_height="wrap_content">
            <com.google.android.material.textfield.TextInputEditText
                android:id="@+id/emailEditText" android:hint="Email"
                android:inputType="textEmailAddress"
                android:layout_width="match_parent" android:layout_height="wrap_content"/>
        </com.google.android.material.textfield.TextInputLayout>

        <com.google.android.material.textfield.TextInputLayout
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:layout_marginTop="12dp">
            <com.google.android.material.textfield.TextInputEditText
                android:id="@+id/passwordEditText" android:hint="Password"
                android:inputType="textPassword"
                android:layout_width="match_parent" android:layout_height="wrap_content"/>
        </com.google.android.material.textfield.TextInputLayout>

        <Button android:id="@+id/loginButton" android:text="LOGIN"
            android:layout_marginTop="20dp" android:layout_width="match_parent"
            android:layout_height="wrap_content"/>

        <Button android:id="@+id/registerButton" android:text="CREATE ACCOUNT"
            android:layout_width="match_parent" android:layout_height="wrap_content"/>

        <TextView android:id="@+id/forgotPasswordText" android:text="Forgot password?"
            android:textColor="@color/eagle_blue" android:gravity="center"
            android:padding="16dp" android:layout_width="match_parent"
            android:layout_height="wrap_content"/>
    </LinearLayout>
</ScrollView>
''',
"app/src/main/res/layout/activity_register.xml": r'''
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent" android:layout_height="match_parent">
    <LinearLayout android:padding="24dp" android:orientation="vertical"
        android:layout_width="match_parent" android:layout_height="wrap_content">
        <TextView android:text="Create Eagle Education Account" android:textSize="26sp"
            android:textStyle="bold" android:textColor="@color/eagle_blue"
            android:layout_marginTop="35dp" android:layout_marginBottom="24dp"
            android:layout_width="wrap_content" android:layout_height="wrap_content"/>
        <EditText android:id="@+id/nameEditText" android:hint="Full Name"
            android:layout_width="match_parent" android:layout_height="wrap_content"/>
        <EditText android:id="@+id/emailEditText" android:hint="Email"
            android:inputType="textEmailAddress" android:layout_marginTop="12dp"
            android:layout_width="match_parent" android:layout_height="wrap_content"/>
        <EditText android:id="@+id/passwordEditText" android:hint="Password"
            android:inputType="textPassword" android:layout_marginTop="12dp"
            android:layout_width="match_parent" android:layout_height="wrap_content"/>
        <EditText android:id="@+id/confirmPasswordEditText" android:hint="Confirm Password"
            android:inputType="textPassword" android:layout_marginTop="12dp"
            android:layout_width="match_parent" android:layout_height="wrap_content"/>
        <Button android:id="@+id/registerButton" android:text="REGISTER"
            android:layout_marginTop="24dp" android:layout_width="match_parent"
            android:layout_height="wrap_content"/>
    </LinearLayout>
</ScrollView>
''',
"app/src/main/res/layout/activity_student_list.xml": r'''
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:padding="16dp"
    android:layout_width="match_parent" android:layout_height="match_parent">
    <LinearLayout android:orientation="horizontal" android:gravity="center_vertical"
        android:layout_width="match_parent" android:layout_height="wrap_content">
        <TextView android:text="Students" android:textSize="28sp" android:textStyle="bold"
            android:textColor="@color/eagle_blue" android:layout_weight="1"
            android:layout_width="0dp" android:layout_height="wrap_content"/>
        <Button android:id="@+id/addStudentButton" android:text="+ ADD"
            android:layout_width="wrap_content" android:layout_height="wrap_content"/>
    </LinearLayout>
    <androidx.recyclerview.widget.RecyclerView android:id="@+id/recyclerView"
        android:layout_marginTop="12dp" android:layout_width="match_parent"
        android:layout_height="0dp" android:layout_weight="1"/>
</LinearLayout>
''',
"app/src/main/res/layout/item_student.xml": r'''
<?xml version="1.0" encoding="utf-8"?>
<com.google.android.material.card.MaterialCardView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent" android:layout_height="wrap_content"
    android:layout_marginBottom="10dp">
    <LinearLayout android:padding="16dp" android:orientation="vertical"
        android:layout_width="match_parent" android:layout_height="wrap_content">
        <TextView android:id="@+id/nameText" android:textSize="19sp" android:textStyle="bold"
            android:textColor="@color/eagle_blue" android:layout_width="match_parent"
            android:layout_height="wrap_content"/>
        <TextView android:id="@+id/detailsText" android:textSize="14sp"
            android:layout_marginTop="5dp" android:layout_width="match_parent"
            android:layout_height="wrap_content"/>
        <LinearLayout android:gravity="end" android:layout_width="match_parent"
            android:layout_height="wrap_content">
            <Button android:id="@+id/editButton" android:text="EDIT"
                android:layout_width="wrap_content" android:layout_height="wrap_content"/>
            <Button android:id="@+id/deleteButton" android:text="DELETE"
                android:layout_width="wrap_content" android:layout_height="wrap_content"/>
        </LinearLayout>
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
''',
"app/src/main/res/layout/activity_add_edit_student.xml": r'''
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent" android:layout_height="match_parent">
    <LinearLayout android:padding="20dp" android:orientation="vertical"
        android:layout_width="match_parent" android:layout_height="wrap_content">
        <TextView android:id="@+id/titleText" android:text="Add Student" android:textSize="27sp"
            android:textStyle="bold" android:textColor="@color/eagle_blue"
            android:layout_marginBottom="20dp" android:layout_width="wrap_content"
            android:layout_height="wrap_content"/>
        <EditText android:id="@+id/nameEditText" android:hint="Student Name"
            android:layout_width="match_parent" android:layout_height="wrap_content"/>
        <EditText android:id="@+id/rollEditText" android:hint="Roll Number"
            android:layout_marginTop="10dp" android:layout_width="match_parent"
            android:layout_height="wrap_content"/>
        <EditText android:id="@+id/classEditText" android:hint="Class / Course"
            android:layout_marginTop="10dp" android:layout_width="match_parent"
            android:layout_height="wrap_content"/>
        <EditText android:id="@+id/phoneEditText" android:hint="Phone"
            android:inputType="phone" android:layout_marginTop="10dp"
            android:layout_width="match_parent" android:layout_height="wrap_content"/>
        <EditText android:id="@+id/emailEditText" android:hint="Student Email"
            android:inputType="textEmailAddress" android:layout_marginTop="10dp"
            android:layout_width="match_parent" android:layout_height="wrap_content"/>
        <EditText android:id="@+id/addressEditText" android:hint="Address"
            android:layout_marginTop="10dp" android:minLines="2"
            android:layout_width="match_parent" android:layout_height="wrap_content"/>
        <Button android:id="@+id/saveButton" android:text="SAVE STUDENT"
            android:layout_marginTop="22dp" android:layout_width="match_parent"
            android:layout_height="wrap_content"/>
    </LinearLayout>
</ScrollView>
''',
"app/src/main/java/com/eagleeducation/app/Student.kt": r'''
package com.eagleeducation.app

data class Student(
    var id: String = "",
    var name: String = "",
    var rollNo: String = "",
    var course: String = "",
    var phone: String = "",
    var email: String = "",
    var address: String = "",
    var createdBy: String = ""
)
''',
"app/src/main/java/com/eagleeducation/app/MainActivity.kt": r'''
package com.eagleeducation.app

import android.content.Intent
import android.os.Bundle
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import com.eagleeducation.app.databinding.ActivityMainBinding
import com.google.firebase.auth.FirebaseAuth

class MainActivity : AppCompatActivity() {
    private lateinit var binding: ActivityMainBinding
    private lateinit var auth: FirebaseAuth

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
        auth = FirebaseAuth.getInstance()

        binding.loginButton.setOnClickListener { login() }
        binding.registerButton.setOnClickListener {
            startActivity(Intent(this, RegisterActivity::class.java))
        }
        binding.forgotPasswordText.setOnClickListener { resetPassword() }
    }

    override fun onStart() {
        super.onStart()
        if (auth.currentUser != null) openStudents()
    }

    private fun login() {
        val email = binding.emailEditText.text.toString().trim()
        val password = binding.passwordEditText.text.toString()
        if (email.isEmpty() || password.isEmpty()) {
            toast("Email and password required"); return
        }
        binding.loginButton.isEnabled = false
        auth.signInWithEmailAndPassword(email, password)
            .addOnSuccessListener { openStudents() }
            .addOnFailureListener { toast(it.message ?: "Login failed") }
            .addOnCompleteListener { binding.loginButton.isEnabled = true }
    }

    private fun resetPassword() {
        val email = binding.emailEditText.text.toString().trim()
        if (email.isEmpty()) { toast("Enter your email first"); return }
        auth.sendPasswordResetEmail(email)
            .addOnSuccessListener { toast("Password reset email sent") }
            .addOnFailureListener { toast(it.message ?: "Unable to send email") }
    }

    private fun openStudents() {
        startActivity(Intent(this, StudentListActivity::class.java))
        finish()
    }

    private fun toast(msg: String) = Toast.makeText(this, msg, Toast.LENGTH_LONG).show()
}
''',
"app/src/main/java/com/eagleeducation/app/RegisterActivity.kt": r'''
package com.eagleeducation.app

import android.os.Bundle
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import com.eagleeducation.app.databinding.ActivityRegisterBinding
import com.google.firebase.auth.FirebaseAuth
import com.google.firebase.firestore.FirebaseFirestore

class RegisterActivity : AppCompatActivity() {
    private lateinit var binding: ActivityRegisterBinding
    private lateinit var auth: FirebaseAuth
    private val db = FirebaseFirestore.getInstance()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityRegisterBinding.inflate(layoutInflater)
        setContentView(binding.root)
        auth = FirebaseAuth.getInstance()

        binding.registerButton.setOnClickListener { register() }
    }

    private fun register() {
        val name = binding.nameEditText.text.toString().trim()
        val email = binding.emailEditText.text.toString().trim()
        val password = binding.passwordEditText.text.toString()
        val confirm = binding.confirmPasswordEditText.text.toString()

        if (name.isEmpty() || email.isEmpty() || password.isEmpty()) {
            toast("All fields are required"); return
        }
        if (password.length < 6) { toast("Password must be at least 6 characters"); return }
        if (password != confirm) { toast("Passwords do not match"); return }

        binding.registerButton.isEnabled = false
        auth.createUserWithEmailAndPassword(email, password)
            .addOnSuccessListener { result ->
                val uid = result.user!!.uid
                val user = hashMapOf(
                    "uid" to uid,
                    "name" to name,
                    "email" to email,
                    "createdAt" to System.currentTimeMillis()
                )
                db.collection("users").document(uid).set(user)
                    .addOnSuccessListener {
                        toast("Account created")
                        finish()
                    }
                    .addOnFailureListener { toast(it.message ?: "Profile save failed") }
            }
            .addOnFailureListener { toast(it.message ?: "Registration failed") }
            .addOnCompleteListener { binding.registerButton.isEnabled = true }
    }

    private fun toast(msg: String) = Toast.makeText(this, msg, Toast.LENGTH_LONG).show()
}
''',
"app/src/main/java/com/eagleeducation/app/StudentListActivity.kt": r'''
package com.eagleeducation.app

import android.content.Intent
import android.os.Bundle
import android.widget.Toast
import androidx.appcompat.app.AlertDialog
import androidx.appcompat.app.AppCompatActivity
import androidx.recyclerview.widget.LinearLayoutManager
import com.eagleeducation.app.databinding.ActivityStudentListBinding
import com.google.firebase.auth.FirebaseAuth
import com.google.firebase.firestore.FirebaseFirestore

class StudentListActivity : AppCompatActivity() {
    private lateinit var binding: ActivityStudentListBinding
    private val db = FirebaseFirestore.getInstance()
    private val auth = FirebaseAuth.getInstance()
    private val students = mutableListOf<Student>()
    private lateinit var adapter: StudentAdapter

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityStudentListBinding.inflate(layoutInflater)
        setContentView(binding.root)

        adapter = StudentAdapter(students,
            onEdit = { student ->
                startActivity(Intent(this, AddEditStudentActivity::class.java).apply {
                    putExtra("studentId", student.id)
                    putExtra("studentName", student.name)
                    putExtra("rollNo", student.rollNo)
                    putExtra("course", student.course)
                    putExtra("phone", student.phone)
                    putExtra("email", student.email)
                    putExtra("address", student.address)
                })
            },
            onDelete = { student -> confirmDelete(student) }
        )
        binding.recyclerView.layoutManager = LinearLayoutManager(this)
        binding.recyclerView.adapter = adapter
        binding.addStudentButton.setOnClickListener {
            startActivity(Intent(this, AddEditStudentActivity::class.java))
        }
    }

    override fun onResume() {
        super.onResume()
        loadStudents()
    }

    private fun loadStudents() {
        val uid = auth.currentUser?.uid ?: run {
            goLogin(); return
        }
        db.collection("students")
            .whereEqualTo("createdBy", uid)
            .get()
            .addOnSuccessListener { snapshot ->
                students.clear()
                snapshot.documents.forEach { doc ->
                    doc.toObject(Student::class.java)?.let {
                        it.id = doc.id
                        students.add(it)
                    }
                }
                students.sortBy { it.name.lowercase() }
                adapter.notifyDataSetChanged()
            }
            .addOnFailureListener { toast(it.message ?: "Could not load students") }
    }

    private fun confirmDelete(student: Student) {
        AlertDialog.Builder(this)
            .setTitle("Delete student?")
            .setMessage(student.name)
            .setNegativeButton("Cancel", null)
            .setPositiveButton("Delete") { _, _ ->
                db.collection("students").document(student.id).delete()
                    .addOnSuccessListener { loadStudents() }
                    .addOnFailureListener { toast(it.message ?: "Delete failed") }
            }.show()
    }

    private fun goLogin() {
        startActivity(Intent(this, MainActivity::class.java))
        finish()
    }

    override fun onCreateOptionsMenu(menu: android.view.Menu): Boolean {
        menu.add("Logout").setShowAsAction(android.view.MenuItem.SHOW_AS_ACTION_NEVER)
        return true
    }

    override fun onOptionsItemSelected(item: android.view.MenuItem): Boolean {
        if (item.title == "Logout") {
            auth.signOut()
            goLogin()
            return true
        }
        return super.onOptionsItemSelected(item)
    }

    private fun toast(msg: String) = Toast.makeText(this, msg, Toast.LENGTH_LONG).show()
}
''',
"app/src/main/java/com/eagleeducation/app/StudentAdapter.kt": r'''
package com.eagleeducation.app

import android.view.LayoutInflater
import android.view.ViewGroup
import androidx.recyclerview.widget.RecyclerView
import com.eagleeducation.app.databinding.ItemStudentBinding

class StudentAdapter(
    private val items: List<Student>,
    private val onEdit: (Student) -> Unit,
    private val onDelete: (Student) -> Unit
) : RecyclerView.Adapter<StudentAdapter.VH>() {

    class VH(val binding: ItemStudentBinding) : RecyclerView.ViewHolder(binding.root)

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): VH {
        return VH(ItemStudentBinding.inflate(LayoutInflater.from(parent.context), parent, false))
    }

    override fun onBindViewHolder(holder: VH, position: Int) {
        val s = items[position]
        holder.binding.nameText.text = s.name
        holder.binding.detailsText.text =
            "Roll: ${s.rollNo}  •  ${s.course}\nPhone: ${s.phone}\nEmail: ${s.email}"
        holder.binding.editButton.setOnClickListener { onEdit(s) }
        holder.binding.deleteButton.setOnClickListener { onDelete(s) }
    }

    override fun getItemCount() = items.size
}
''',
"app/src/main/java/com/eagleeducation/app/AddEditStudentActivity.kt": r'''
package com.eagleeducation.app

import android.os.Bundle
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import com.eagleeducation.app.databinding.ActivityAddEditStudentBinding
import com.google.firebase.auth.FirebaseAuth
import com.google.firebase.firestore.FirebaseFirestore

class AddEditStudentActivity : AppCompatActivity() {
    private lateinit var binding: ActivityAddEditStudentBinding
    private val db = FirebaseFirestore.getInstance()
    private val auth = FirebaseAuth.getInstance()
    private var studentId: String? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityAddEditStudentBinding.inflate(layoutInflater)
        setContentView(binding.root)

        studentId = intent.getStringExtra("studentId")
        if (studentId != null) {
            binding.titleText.text = "Edit Student"
            binding.nameEditText.setText(intent.getStringExtra("studentName"))
            binding.rollEditText.setText(intent.getStringExtra("rollNo"))
            binding.classEditText.setText(intent.getStringExtra("course"))
            binding.phoneEditText.setText(intent.getStringExtra("phone"))
            binding.emailEditText.setText(intent.getStringExtra("email"))
            binding.addressEditText.setText(intent.getStringExtra("address"))
        }

        binding.saveButton.setOnClickListener { saveStudent() }
    }

    private fun saveStudent() {
        val uid = auth.currentUser?.uid ?: return
        val name = binding.nameEditText.text.toString().trim()
        if (name.isEmpty()) { toast("Student name is required"); return }

        val data = hashMapOf(
            "name" to name,
            "rollNo" to binding.rollEditText.text.toString().trim(),
            "course" to binding.classEditText.text.toString().trim(),
            "phone" to binding.phoneEditText.text.toString().trim(),
            "email" to binding.emailEditText.text.toString().trim(),
            "address" to binding.addressEditText.text.toString().trim(),
            "createdBy" to uid,
            "updatedAt" to System.currentTimeMillis()
        )

        binding.saveButton.isEnabled = false
        val task = if (studentId == null) {
            db.collection("students").add(data)
        } else {
            db.collection("students").document(studentId!!).set(data)
        }

        task.addOnSuccessListener {
            toast(if (studentId == null) "Student added" else "Student updated")
            finish()
        }.addOnFailureListener {
            toast(it.message ?: "Save failed")
        }.addOnCompleteListener {
            binding.saveButton.isEnabled = true
        }
    }

    private fun toast(msg: String) = Toast.makeText(this, msg, Toast.LENGTH_LONG).show()
}
''',
"firestore.rules": r'''
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    match /users/{userId} {
      allow read, create, update: if request.auth != null
        && request.auth.uid == userId;
      allow delete: if false;
    }

    match /students/{studentId} {
      allow read: if request.auth != null
        && resource.data.createdBy == request.auth.uid;

      allow create: if request.auth != null
        && request.resource.data.createdBy == request.auth.uid;

      allow update: if request.auth != null
        && resource.data.createdBy == request.auth.uid
        && request.resource.data.createdBy == request.auth.uid;

      allow delete: if request.auth != null
        && resource.data.createdBy == request.auth.uid;
    }
  }
}
''',
"README.md": r'''
# Eagle Education — Kotlin + Firebase

Included:
- Firebase Email/Password Login
- Registration
- Forgot Password
- Firestore user profile
- Student database CRUD
- Student list with Edit/Delete
- Per-user Firestore security rules
- Material UI + RecyclerView

## Firebase setup

1. Create a Firebase project.
2. Add an Android app with package name:
   `com.eagleeducation.app`
3. Download `google-playstore.json`.
5. Put it at:
   `app/google-services.json`
6. In Firebase Authentication, enable Email/Password.
7. Create a Cloud Firestore database.
8. Publish the included `firestore.rules`.

## Open in Android Studio

Open the `EagleEducation` folder and let Gradle sync.

Firebase docs:
https://firebase.google.com/docs/android/setup
https://firebase.google.com/docs/auth/android/password-auth
https://firebase.google.com/docs/firestore/manage-data/add-data
'''
