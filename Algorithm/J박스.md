# 백준 5354 J박스 

## 문제


> [백준 5354 J박스 ][백준 5354 J박스 ]

[백준 5354 J박스 ]: https://www.acmicpc.net/problem/5354
* * *
<br>

## 나의 답
```java
import java.util.Arrays;
import java.util.Scanner;

public class JBOX {

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        int n = sc.nextInt();
        int[] arr = new int[n];
        for (int i = 0; i < arr.length; i++) {
            arr[i] = sc.nextInt();
        }

        for (int i = 0; i < arr.length; i++) {  
            for (int j = 0; j < arr[i]; j++) {  
                for (int k = 0; k < arr[i]; k++) {  
                    if (j == 0 || j == arr[i] - 1 || k == 0 || k == arr[i]-1) {
                        System.out.print("#");
                    } else if (k != 0 || k != arr[i] - 1) {
                        System.out.print("J");
                    }
                }
                System.out.println();
            }
            System.out.println();
        }
    }
}
```
