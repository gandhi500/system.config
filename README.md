OS - 1

```
#include <stdio.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <time.h>
#include<stdlib.h>

int main() {
    char filename[100];
    struct stat fileStat;

    printf("Enter the file path: ");
    scanf("%s", filename);

    // Get file statistics
    // stat() function is used to list properties of a file identified by path . It reads all file properties and dumps to buf structure. The function is defined in sys/stat. h header file.
    if (stat(filename, &fileStat) < 0) {
        perror("Error");
        return 1;
    }

    // Print file properties
    printf("File Properties:\n");
    // The file serial number, which distinguishes this file from all other files on the same device.
    printf("Inode number: %ld\n", fileStat.st_ino);
    // The number of hard links to the file. This count keeps track of how many directories have entries for this file. If the count is ever decremented to zero, then the file itself is discarded as soon as no process still holds it open. Symbolic links are not counted in the total.
    printf("Number of hard links: %ld\n", fileStat.st_nlink);
    // st_mode Specifies the mode of the file. This includes file type information (see Testing the Type of a File) and the file permission bits (see The Mode Bits for Access Permission).
    // S_IRWXU This is equivalent to read, write and execute
    // S_IRGRP
//Read permission bit for the group owner of the file. Usually 040.

//S_IWGRP
//Write permission bit for the group owner of the file. Usually 020.

//S_IXGRP
//Execute or search permission bit for the group owner of the file. Usually 010.

//S_IRWXG
//This is equivalent to ‘(S_IRGRP | S_IWGRP | S_IXGRP)’.
//S_IRGRP
//Read permission bit for the group owner of the file. Usually 040.

//S_IWGRP
//Write permission bit for the group owner of the file. Usually 020.

//S_IXGRP
//Execute or search permission bit for the group owner of the file. Usually 010.

//S_IRWXG
//This is equivalent to ‘(S_IRGRP | S_IWGRP | S_IXGRP)’.
    printf("File permissions: %o\n", fileStat.st_mode & (S_IRWXU | S_IRWXG | S_IRWXO));
    // This specifies the size of a regular file in bytes. For files that are really devices this field isn’t usually meaningful. For symbolic links this specifies the length of the file name the link refers to.
    printf("File size: %ld bytes\n", fileStat.st_size);
    //This is the last access time for the file. See File Times.
    printf("Last access time: %s", ctime(&fileStat.st_atime));
    //This is the time of the last modification to the contents of the file. See File Times.
    printf("Last modification time: %s", ctime(&fileStat.st_mtime));

    return 0;
}
```

OS - 2

```
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <sys/times.h>
#include <unistd.h>

int main() {
    int n;
    printf("Enter the number of child processes: ");
    scanf("%d", &n);

    struct tms start_time, end_time;
    clock_t start_clock, end_clock;
    double user_time = 0, kernel_time = 0;

    for (int i = 0; i < n; i++) {
    //The fork() is used for creating a new copy of the calling function. The newly created process is known as the Child process and the process from which the child process is created is known as the parent process.
        pid_t pid = fork();

        if (pid == 0) {
            // Child process
            exit(0);
        } else if (pid > 0) {
            // Parent process
            continue;
        } else {
        //The perror() function prints an error message to stderr . If string is not NULL and does not point to a null character, the string pointed to by string is printed to the standard error stream, followed by a colon and a space
            perror("fork");
            exit(1);
        }
    }

    // Wait for all child processes to terminate
    int status;
    pid_t child_pid;
    while ((child_pid = wait(&status)) != -1) {
    // WIFEXITED–Query status to see if a child process ended normally. This macro queries the child termination status provided by the wait and waitpid functions, and determines whether the child process ended normally.
        if (WIFEXITED(status)) {
            printf("Child process %d terminated.\n", child_pid);
        }
    }

    // Calculate total cumulative time spent by children
    // Please note that the times() function returns the process times in clock ticks, so we divide them by sysconf(_SC_CLK_TCK) to convert them into seconds. Also, keep in mind that the actual time spent in user and kernel mode may vary depending on the system's scheduling and other factors.
    start_clock = times(&start_time);
    for (int i = 0; i < n; i++) {
        end_clock = times(&end_time);
        user_time += (double)(end_time.tms_utime - start_time.tms_utime) / sysconf(_SC_CLK_TCK);
        kernel_time += (double)(end_time.tms_stime - start_time.tms_stime) / sysconf(_SC_CLK_TCK);
        start_time = end_time;
    }

    printf("Total cumulative time spent by children:\n");
    printf("User mode: %.2lf seconds\n", user_time);
    printf("Kernel mode: %.2lf seconds\n", kernel_time);

    return 0;
}

OR


#include<sys/types.h>
#include<sys/wait.h>
#include<unistd.h>
#include<time.h>
#include<sys/times.h>
#include<stdio.h>
#include<stdlib.h>
int main(void)
{
int i, status;
 pid_t pid;
 time_t currentTime;
struct tms cpuTime;
if((pid = fork())==-1) //start child process
 {
 perror("\nfork error");
 exit(EXIT_FAILURE);
 }
else if(pid==0) //child process
 {
 time(&currentTime);
 printf("\nChild process started at %s",ctime(&currentTime));
 for(i=0;i<5;i++)
 {
 printf("\nCounting= %dn",i); //count for 5 seconds
 sleep(1);
 }
 time(&currentTime);
 printf("\nChild process ended at %s",ctime(&currentTime));
 exit(EXIT_SUCCESS);
 }
else
 { //Parent process
 time(&currentTime); // gives normal time
 printf("\nParent process started at %s ",ctime(&currentTime));
 if(wait(&status)== -1) //wait for child process
 perror("\n wait error");
 if(WIFEXITED(status))
 printf("\nChild process ended normally");
 else
 printf("\nChild process did not end normally");
 if(times(&cpuTime)<0) //Get process time
 perror("\nTimes error");
 else
 { // _SC_CLK_TCK: system configuration time: seconds clock tick
 printf("\nParent process user time= %fn",((double)
cpuTime.tms_utime));
 printf("\nParent process system time = %fn",((double)
cpuTime.tms_stime));
printf("\nChild process user time = %fn",((double)
cpuTime.tms_cutime));
 printf("\nChild process system time = %fn",((double)
cpuTime.tms_cstime));
 }
 time(&currentTime);
 printf("\nParent process ended at %s",ctime(&currentTime));
 exit(EXIT_SUCCESS);
 }
}
/* ---------------------------------------------------------------------------------------------------
OUTPUT (as per system)
Parent process started at Sun Mar 14 13:18:46 2021
Child process started at Sun Mar 14 13:18:46 2021
Counting= 0n
Counting= 1n
Counting= 2n
Counting= 3n
Counting= 4n
Child process ended at Sun Mar 14 13:18:51 2021
Child process ended normally
Parent process user time= 1.000000n
Parent process system time = 0.500000n
Child process user time = 2.000000n
Child process system time = 1.200000n
Parent process ended at Sun Mar 14 13:18:51 2021
*/
```

MT - 1
XML MAIN - 1

``
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/editTextNumber1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Enter number 1"
        android:inputType="number" />

    <EditText
        android:id="@+id/editTextNumber2"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Enter number 2"
        android:inputType="number" />

    <Button
        android:id="@+id/buttonPower"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Calculate Power" />

    <Button
        android:id="@+id/buttonAverage"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Calculate Average" />

</LinearLayout>
```

XML - 2

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp"
    tools:context=".ResultActivity">

    <TextView
        android:id="@+id/textViewResult"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:text="Result"
        android:textSize="24sp" />

</LinearLayout>
```

JAVA - 1

```
editTextNumber1 = findViewById(R.id.editTextNumber1);
        editTextNumber2 = findViewById(R.id.editTextNumber2);
        buttonPower = findViewById(R.id.buttonPower);
        buttonAverage = findViewById(R.id.buttonAverage);

        buttonPower.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                double number1 = Double.parseDouble(editTextNumber1.getText().toString());
                double number2 = Double.parseDouble(editTextNumber2.getText().toString());
                double result = Math.pow(number1, number2);

                Intent intent = new Intent(MainActivity.this, ResultActivity.class);
                intent.putExtra("result", String.valueOf(result));
                startActivity(intent);
            }
        });

        buttonAverage.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                double number1 = Double.parseDouble(editTextNumber1.getText().toString());
                double number2 = Double.parseDouble(editTextNumber2.getText().toString());
                double result = (number1 + number2) / 2;

                Intent intent = new Intent(MainActivity.this, ResultActivity.class);
                intent.putExtra("result", String.valueOf(result));
                startActivity(intent);
            }
        });
        
```
        
JAVA - 2

```
package com.maverick.average_power;

import androidx.appcompat.app.AppCompatActivity;

import android.os.Bundle;
import android.widget.TextView;

public class ResultActivity extends AppCompatActivity {
    private TextView textViewResult;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_result);

        textViewResult = findViewById(R.id.textViewResult);

        String result = getIntent().getStringExtra("result");
        textViewResult.setText("Result: " + result);
    }
}
```

MT - 2
XML

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/editTextString"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Enter a string" />

    <RadioGroup
        android:id="@+id/radioGroupOperations"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <RadioButton
            android:id="@+id/radioButtonUppercase"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Uppercase" />

        <RadioButton
            android:id="@+id/radioButtonLowercase"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Lowercase" />

        <RadioButton
            android:id="@+id/radioButtonRight5"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Right 5 characters" />

        <RadioButton
            android:id="@+id/radioButtonLeft5"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Left 5 characters" />

    </RadioGroup>

    <Button
        android:id="@+id/buttonPerformOperation"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Perform Operation" />

    <TextView
        android:id="@+id/result"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:text="Result"
        android:textSize="25sp"/>

</LinearLayout>
```

JAVA

```
editTextString = findViewById(R.id.editTextString);
        radioGroupOperations = findViewById(R.id.radioGroupOperations);
        buttonPerformOperation = findViewById(R.id.buttonPerformOperation);
        resultTextView = findViewById(R.id.result);

        buttonPerformOperation.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                String inputString = editTextString.getText().toString();
                int selectedId = radioGroupOperations.getCheckedRadioButtonId();

                if (selectedId == -1) {
                    Toast.makeText(MainActivity.this, "Please select an operation", Toast.LENGTH_SHORT).show();
                    return;
                }

                RadioButton radioButton = findViewById(selectedId);
                String operation = radioButton.getText().toString();

                String result = "";

                switch (operation) {
                    case "Uppercase":
                        result = inputString.toUpperCase();
                        break;
                    case "Lowercase":
                        result = inputString.toLowerCase();
                        break;
                    case "Right 5 characters":
                        if (inputString.length() > 5) {
                            result = inputString.substring(inputString.length() - 5);
                        } else {
                            result = inputString;
                        }
                        break;
                    case "Left 5 characters":
                        if (inputString.length() > 5) {
                            result = inputString.substring(0, 5);
                        } else {
                            result = inputString;
                        }
                        break;
                }

                resultTextView.setText(result);

                Toast.makeText(MainActivity.this, "Result: " + result, Toast.LENGTH_SHORT).show();
            }
        });
```
