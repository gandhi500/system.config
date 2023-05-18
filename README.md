<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <LinearLayout
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_margin="10dp"
        android:layout_height="wrap_content">

        <EditText
            android:id="@+id/et_id"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="Id"
            android:inputType="number"
            />

        <EditText
            android:id="@+id/et_name"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:inputType="textPersonName"
            android:hint="name"
            />
        <EditText
            android:id="@+id/et_address"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:inputType="textPostalAddress"
            android:hint="Address"
            />

        <EditText
            android:id="@+id/et_phone"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:inputType="phone"
            android:hint="Phone Number"
            />

        <Button
            android:id="@+id/bt_insert"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="insert company details"/>

        <Button
            android:id="@+id/bt_view_all"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="view all company details"/>


        <TextView
            android:id="@+id/tv_output"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Output"
            android:layout_marginTop="30dp"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent" />

    </LinearLayout>


</RelativeLayout>
