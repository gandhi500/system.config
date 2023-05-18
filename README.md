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
    if (stat(filename, &fileStat) < 0) {
        perror("Error");
        return 1;
    }

    // Print file properties
    printf("File Properties:\n");
    printf("Inode number: %ld\n", fileStat.st_ino);
    printf("Number of hard links: %ld\n", fileStat.st_nlink);
    printf("File permissions: %o\n", fileStat.st_mode & (S_IRWXU | S_IRWXG | S_IRWXO));
    printf("File size: %ld bytes\n", fileStat.st_size);
    printf("Last access time: %s", ctime(&fileStat.st_atime));
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
        pid_t pid = fork();

        if (pid == 0) {
            // Child process
            exit(0);
        } else if (pid > 0) {
            // Parent process
            continue;
        } else {
            perror("fork");
            exit(1);
        }
    }

    // Wait for all child processes to terminate
    int status;
    pid_t child_pid;
    while ((child_pid = wait(&status)) != -1) {
        if (WIFEXITED(status)) {
            printf("Child process %d terminated.\n", child_pid);
        }
    }

    // Calculate total cumulative time spent by children
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
