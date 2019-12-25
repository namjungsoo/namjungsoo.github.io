---
layout: post
title: Android 쓰레드에서 다이얼로그 띄우기
---
안드로이드에서 쓰레드 루프 중에 쓰레드를 멈추고 modal dialog를 받는 방법을 예제로 만들어 보았다. 동기화에 사용되는 오브젝트는 object lock으로 wait, notify를 사용하였다.

```java
package com.namjungsoo.www.androidthreadtest;

import android.content.DialogInterface;
import android.support.v7.app.AlertDialog;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.KeyEvent;
import android.view.View;
import android.widget.Button;

public class MainActivity extends AppCompatActivity {
    Object lock = new Object();

    void showAlert() {
        AlertDialog.Builder builder = new AlertDialog.Builder(MainActivity.this);
        AlertDialog dialog = builder
                .setTitle("title")
                .setMessage("message")
                .setPositiveButton("ok", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialogInterface, int i) {
                        releaseLock();
                    }
                })
                .setNegativeButton("cancel", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialogInterface, int i) {
                        releaseLock();
                    }
                })
                .setNeutralButton("skip", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialogInterface, int i) {
                        releaseLock();
                    }
                })
                .setCancelable(false)// 백버튼 불가, 바탕화면 클릭 불가 
                .create();
        dialog.show();
    }

    void releaseLock() {
        synchronized (lock) {
            lock.notify();
            Log.e("TAG", "notify");
        }
    }

    Runnable runnable;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        runnable = new Runnable() {
            @Override
            public void run() {
                try {
                    runOnUiThread(new Runnable() {
                        @Override
                        public void run() {
                            showAlert();
                        }
                    });
                    Log.e("TAG", "before wait");
                    synchronized (lock) {
                        lock.wait();
                    }
                    Log.e("TAG", "after wait");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        };

        Button btn = (Button) findViewById(R.id.hello);
        btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                new Thread(runnable).start();
            }
        });

    }
}
```
