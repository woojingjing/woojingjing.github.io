# FFT(fast fourier transform)

### **푸리에 변환** 
- 시간에 대한 함수(신호)를 주파수 성분으로 분해하여 변환한 것을 말한다. 주파수에 관점에서 신호를 볼 수 있으며, 불필요한 주파수 성분을 제거할 수도 있다.<br>

- *여러 주파수가 합쳐져 있는 신호* --> *푸리에 변환* --> *각각의 주파수로 나타냄* --> *불필요한 주파수 제거* --> *역 푸리에 변환* --> *필요한 주파수로만 신호로 나타냄*

![푸리에 변환](https://github.com/woojingjing/woojingjing.github.io/blob/main/%EC%BA%A1%EC%B2%98%202023-05-04%20092228.png)

나라 간 핵 군비 경쟁 중 비밀리에 지하에서 진행되는 핵실험, 지하 핵폭파의 주파수를 감지하기 위해, 즉 핵실험과 지진을 구분하기 위해 푸리에 변환을 사용하였다.<br>
처음에는 지진파 신호를 무한한 연속파로 가정하였고 푸리에 변환은 무한한 연속주파수 스펙트럼을 얻었다. 그러나 실제 지진파 신호는 유한하며 연속파가 아닌 하나하나의 데이터 포인트들로 이루어져 있었다. (지진계 신호가 연속적으로 보일순 있어도 무한한 정밀도로 기록하지는 못한다)
따라서, 이상적 푸리에 변환은 불가능했고 대신 **이산 푸리에 변환**을 사용하기 시작한다. <br>
- 실제 지진파 신호
![실제 지진파 신호](https://github.com/woojingjing/woojingjing.github.io/blob/main/%EC%BA%A1%EC%B2%98%202023-05-04%20094410.png)

### **DFT(Discrete fourier transform): 이산푸리에변환. (N^2)**
- 주파수 스펙트럼이 이산적이고 유한하다.<br>
- 샘플 간의  간격이 넓을수록, 측정할 수 있는 최대 주파수는 낮아진다. 간격이 넓은 샘플로는 고주파수 진동을 포착 못한다.<br>
샘플간의 간격이 짧을수록, 유사한 주파수를 구분짓기 어려워지고 주파수 분해능이 낮아진다.<br>
- 8개의 샘플(점의 개수) ---- 이산 푸리에 변환 --->  8개의 주파수 bin(신호에 각각 sin과 cos 곱의 총 면적의 합)<br>
- 하나의 이산 푸리에 변환을 위해 N개의 데이터 포인트의 N개의 서로 다른 주파수를 곱해야 하므로(N^2) 숫자가 크면 클수록 많은 계산에 따른 속도가 저하가 한계이다.

*DFT 연산을 획기적으로 줄여 효율적인 계산을 할 수 있도록 만든게 FFT(N * log N)* 

- DFT와 FFT의 시간 복잡도 비교 (N=1024 일 때)
```java
import java.awt.*;
import java.util.*;
import java.util.Scanner;

public class Main{

    public static int DFT(int N) {
        int count = 0;
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < N; j++) {
                count++;
            }
        }
        return count;
    }

    public static void main(String[] args) {
        int N = 1024;
        int count = DFT(N);
        System.out.println("DFT 시간 복잡도= " + count);
    }
}
```
- 결과: DFT 시간 복잡도 = 1048576

```java
import java.awt.*;
import java.util.*;
import java.util.Scanner;

public class Main{

    public static int FFT(int N) {
        int count = 0;
        for (int i = 1; i < N; i *= 2) {
            count += i * (int) (Math.log(i) / Math.log(Math.E));
        }
        return count;
    }

    public static void main(String[] args) {
        int N = 1024;
        int count = FFT(N);
        System.out.println("FFT 시간 복잡도= " + count);
    }
}
```
- 결과: FFT 시간 복잡도 = 5268

**N 이 크면 클수록(데이터의 양이 많을수록) FFT가 월등히 빠르다.**

### **FFT(Fast fourier transform): 고속푸리에변환. (N * log N)**
- DFT 빠르게 하기 위한 알고리즘, 복잡한 신호를 각각 고유한 진폭과 주파수를 가진 순수 사인파들로 분해하는 방법이다.<br>
- sin 앞쪽 짝수 4개의 주파수와  나머지 짝수 4개의 주파수를 비교했을 때, 모든 데이터 포인트의 위치에서 두 사인파의 값이 동일하다.<br>
sin 앞쪽 홀수 4개의 주파수와  나머지 홀수 4개의 주파수를 비교했을 때, 모든 데이터 포인트의 위치에서 두 사인파의 값은 한 사인파의 값이 다른 사인파의 음수값을 가진다=> *복소수 특성*<br> 
따라서 앞쪽 4개의 주파수만 계산하고 곱하기 2 하면 된다. **필요한 계산횟수 절반으로 줄임** 

![고속푸리에변환](https://github.com/woojingjing/woojingjing.github.io/blob/main/%EC%BA%A1%EC%B2%98%202023-05-04%20144648.png)

- FFT에서 중요한 것
1. 데이터 갯수
2. 주파수(데이터 샘플의 간격)
3. 적분(컨볼루션)<br>
-임의로 여러가지 신호를 흘려, 어떤 주파수 성분이 많이 들어있는지 알기 위함.
4. 오일러 공식(결과 = 복소수)<br>
-sin x , sin x+2ㅠ , cos x, cos x+2ㅠ 총 4 개의 주파수를 이용해서 성분의 적분값을 얻기에는 너무 복잡하다.<br>
그래서 이 4개를 동시에 할 수 있는 오일러 공식 e^{ix}=cos x + i*sin x 를 이용하여 좀 더 편하게 구할 수 있다. 결과값은 복소수가 나온다. (실수부는 코사인파의 진폭, 허수부는 사인파의 진폭)


#### **FFT의 활용 분야**
- 음성 신호 처리 : FFT를 이용하여 주파수 성분을 추출하고 필터링하여 잡음을 제거하거나 음성 인식에 활용 될 수 있다. EX)전자 비서 시스템(시리, 빅스비 등등)
- 뇌파 측정 : 뇌파 신호의 진동수에 따라 분류할 때, 관찰 결과 속에는 환자의 호흡, 심작박동등 에 따른 여러가지 파동들이 복잡하게 섞여 있어 FFT 이용하여 순수한 뇌파만을 얻을 수 있다. 
- 자율주행자동차 센서 : FFT를 이용하여 수집한 신호를 주파수 영역으로 변환하여 주파수 성분별로 분석할 수 있다. 이를 통해, 자동차 주변의 환경 정보를 추출하고, 주행 상황에 따라 적절한 조작을 수행할 수 있다.<br>
이 외에도 통신, 신호처리, 생의학 영상공학, 음향학 등등 여러 분야에서 활용되고 있다.<br>
(교수님 FFT 구현은 너무 어려워서 구현을 못했습니다. 죄송합니다...)
