# Understanding of Microfacet And Microflake

In microfacet models, a detailed microsurface is replaced by a simplified macrosurface with a modified scattering function (BSDF) that matches the aggregate directional scattering of the microsurface. This assumes that micro surface detail is too small to be seen directly, so only the far-field directional scattering pattern matters. {B. Walter et al]

## Microfacet 이론
BSDF는 면에 빛이 어떻게 산란이 되는지를 묘사하는 함수이다. 다시 설명하자면, 입사방향 $i$의 irradiance와 방사방향 $o$의 radiance의 비율을 정의한다. 이 관계를 함수로 표현하면 $f_s(i,o,n)$이다. 반사만 구성된다면 BRDF, 투과는 BTDF로 말한다. 여기서는 반사만 이해하고 설명하겠다.

Microfacet 모델은 촘촘한 microsurface들을 단순macrosurface로 대체하고 산란 함수를 재정의한다. 이 재정의한 산란 함수는 microsurface의 방향성 산란들을 하나의 함수로 표현하여 쉽게 접근하여 모델을 정의하는 이론이다. 이 가정은 미세면들이 보이지 않을정도로 작아서, 거시적 관점에서 산란 방향 패턴을 분석하여 모델화 한다. 보통 기하학적으로 광학요소와 단일 산란(Single Scattering)만 모델화한다. microsurface내에서의 파장효과이나 2번이상 반사하는 등 복잡한 요소는 무시한다.
>&emsp;2번이상 반사 즉, Multiple Scattering는 [E. Heitz et al]에 연구되었다.

![](pic/umm_f1.png "그림1") 그림1

### 함수항 D, G
D항은 microfacet 분포함수이고, G는 microfacet의 시각적 영향을 주는 비율값을 주는 함수 이다.

### 이상적 반사BRDF(Ideal Reflection)
렌더링식에서 완벽한 평변 반사를 표현하는 BRDF를 대입하면,
$$
L(o) = \int_{\Omega}f_r(i,o)L_i(i)|n\cdot i|d\omega_i = F_r(r)L_i(r)
$$

완벽한 평변 반시이기 때문에, 반사방향은 입사방향 $i$가 있으면 반사방향은 $r$로 고정이 된다. 이 식을 Dirac 델타함수를 써서 적분을 단순화 할 수 있다. Dirac 델타함수는 수학적인 함수가 아닌 일반화된 함수로 단순화된 식으로 유도할 수 있다.

$$
L(o)=\int\delta(i-r)F_r(i)L_i(i)[n \cdot i|d\omega_i = F_r(r)L_i(r)|n \cdot i|
$$

그리고 완벽한 평면 반사를 표현하기에는 $|n \cdot i|$이 빠져야 한다. 그러므로,
$$
f_r(o,i)=F_r(r)\frac{\delta(i-r)}{|n \cdot i|}
$$

으로 유도된다.
>Dirac 델타 함수를 사용해서 적분식을 제거하고 식을 단순화 하는데 사용되었다.

### Macrosurface BSDF Integral
Macrosurface BSDF모델은 미시적관점에서 이뤄지는 산란의 합 들로 구성한다. 즉 Microsurface단계에서 보이는 모든 시각적인 영향을 단일모델로 표현가능하게 총합(적분)하여 계산하게 한다. 미세면들의 노멀을 $m$ 으로 정의하고 시각적으로 전달되는 모든 면들을 D항 G항의 곱으로 표현한다. 정확한 요소를 적용하기 위해 미세면들로 입사하는 irradiance량을 microsurface로 변환하고 산란된 radiance들을 다시 macrosurface로 변환하려 돌려준다.
$$
f_s(i,o,n) = \int |\frac{i \cdot m}{i \cdot n}| f_s^m(i,o,m) |\frac{o \cdot m}{o \cdot n}|G(i,o,m)D(m)d\omega_m \quad(1)
$$
위 수식의 의미를 풀어쓴다면,

$$
f_s(i,o,n)=\int(i를\,m으로\,변환)\cdot(m의\,반사와\,투과함수)\cdot(o를\,m으로\,변환)\cdot G \cdot D \cdot 미소방향m
$$

>$i$와 $o$를 $m$으로 변환이라고 풀어 썼지만, 좀더 정확하게 말하면 각각 $i,o$를 노멀$n$ 으로 입사량과 microsurface 노멀$m$ 과 입사량의 기하학적인 관계를 말한다

$f_s^m(i,o,m)$은 이상적인(ideal) 반사와 투과 함수로 정의하고, 본 문서에서는 반사만 다룬다.

## Microsurface Specualr BSDFs
다시한번 언급하면 반반한(flat) 거울면들의 집합체로 미세면들을 정의하고, 이 면들을 거시적 관점으로 전환하여 모델화 시킨것이 Microfacet 분포 모델이라고 할 수 있다. 입사하는 에너지량($\rho$)이 입사방향($i$)에서 단일 스페큘러 반사 방향($o$)일때, 스페큘러 BSDF를 다음과 정의한다.
$$
f_s^m(i,o,m)=\rho\frac{\delta_{\omega_o}(s, o)}{|o \cdot m|}
$$

$o$방향 입체각(solid-angle)을 측정량을 $d\omega_{o}$로 표현하고, 이 측정량으로 적분을 하면 다음 과 같이 된다:
$$
\int_{\Omega}g(o)\delta_{\omega_{o}}(s,o)d\omega_{o}=\bigg\{\genfrac{}{}{0pt}{}{g(s)\quad if \in\Omega}{0\quad otherwise}
$$

[식(1)]에서 위BSDF를 사용하려면, microsurface의 노멀과 그에 맞는 입체각 변환을 도입해야한다.
먼저 입사방향과 방사방향이 주어진 상황에서 단일 microsurface노멀을 계산 할 수 있다. $i$에서 $o$로 전환될때 에너지가 산란되고, 여기서 미시적으로 노멀 역활할 microsurface노멀을 정의한다. 이 노멀은 half-vector이다. 그다음 $h$와 $m$사이의 델타 함수로 다시 BSDF를 만든다:

$$
f_s^m(i,o,m)=\rho(i,m)\frac{\delta_{\omega_{o}}(h(i,o),m)}{|o \cdot m|}||\frac{\partial\omega_{h}}{\partial\omega_{o}}||
$$

여기서 $||\frac{\partial\omega_{h}}{\partial\omega_{o}}||$는 Jocobian 행렬식 절대값이다. 이 Jacobian행렬은 $h$와 $o$간의 변환을 한다. 이 Jocobian은 두 공간의 편차 크기를 말한다. 방향$o$의 미소 입체각$d\omega_{o}$만큼 변화 했을때 $h$방향의 미소 입체각$d\omega_{o}$의 변화를 말한다:

$$
||\frac{\partial\omega_{h}}{\partial\omega_{o}}||=\lim_{d\omega_{o} \to 0}\,\frac{d\omega_{h}}{d\omega_{o}}
$$

입체각은 구면의 투영된 면적에 직접적인 연관을 말한다. 그리고 이 극미량 면적은 평면에 가깝다고 볼 수 있다. 이 극소 입체각$d\omega_{o}$이 극소 입체각$d\omega_{h}$로 변환되는 과정을 기하학적으로 풀어내면 다음과 같다:

$$
||\frac{\partial\omega_{h}}{\partial\omega_{o}}||=\frac{|o \cdot h_{r}|}{||\vec{h_{r}}||^2} \quad(2)
$$

![](pic/umm_f2.png "그림2") 그림2

### $f_r^m$ 이상적 반사(Ideal Reflection)
[식(2)]에서 좀더 풀어나가면 분모는 $o$와$h$간의 반사 벡터의 2제곱으로 다시 풀어쓸 수 있으며,

$$
\frac{|o \cdot h_{r}|}{||\vec{h_{r}}||^2}=\frac{|o \cdot h_{r}|}{(2|o \cdot h_{r}|)^2}=\frac{1}{4|o \cdot h_{r}|}\quad(3)
$$

[식(3)]식에서는 $||\vec{h_r}||=(\vec{h_r} \cdot h_{r})$ 그리고 $(o \cdot h_r)=(i \cdot h_r)$를 사용한다. 그리고 $\rho$는 프레넬 계수 $F$와 같으므로 microsurface BSDF는,

$$
f_r^m(i,o,m)=F(i,m)\frac{\delta_{\omega_m}(h_r,m)}{4(i \cdot h_r)^2}
$$

여기서 Jacobian행렬 도입을 다른 의미로 설명해 보자면, $f_r^m$이 증가하려면$|i \cdot h_r|$이 감소 해야한다. 즉 이 상황은 off specular(정반사외(外) 반사영역)를 연출하는 효과가 나온다.

### $f_t^m$, 이상적 투과(Ideal Refraction)
본 문서에서는 투과부분은 설명하지 않는다. 그래서 $f_s^m(i,o,m)=f_r^m(i,o,m)$이라고 정의하고 넘어간다.

## 거친 표면을 위한 BSDF
$f_s(i,o,m)=f_r(i,o,m) + f_t(i,o,m)$에서 반사만 다루기 때문에, $f_s(i,o,m)=f_r(i,o,m)$만 설명한다. 다시 [식(1)]에서 차근차근 유도해본다면,

$$
f_s(i,o,n) = \int |\frac{i \cdot m}{i \cdot n}| f_s^m(i,o,m) |\frac{o \cdot m}{o \cdot n}|G(i,o,m)D(m)d\omega_m
$$
$$
= \int \frac{|i \cdot m|}{|i \cdot n|} F_r(i,m) \frac{\delta(h_r,m)}{|o \cdot m|} \frac{1}{4|i \cdot h_r|} \frac{|o \cdot m|}{|o \cdot n|}G(i,o,m)D(m)d\omega_m
$$
$$
= \int \frac{|i \cdot m|}{|i \cdot n|} F_r(i,m) \frac{\delta(h_r,m)}{\cancel{|o \cdot m|}} \frac{1}{4|i \cdot h_r|} \frac{\cancel{|o \cdot m|}}{|o \cdot n|}G(i,o,m)D(m)d\omega_m
$$

$$
= \int \frac{|i \cdot m|}{|i \cdot n|} F_r(i,m) \frac{\delta(h_r,m)}{4|i \cdot h_r|} \frac{1}{|o \cdot n|}G(i,o,m)D(m)d\omega_m
$$

Dirac 델타함수로 적분이 제거($m\to h_r$)

$$
= \frac{|i \cdot h_r|}{|i \cdot n|} F_r(i,h_r) \frac{1}{4|i \cdot h_r||o \cdot n|}G(i,o,h_r)D(h_r)
$$
$$
= \frac{\cancel{|i \cdot h_r|}}{|i \cdot n|} F_r(i,h_r) \frac{1}{4\cancel{|i \cdot h_r|}|o \cdot n|}G(i,o,h_r)D(h_r)
$$

정리를하면,

$$
f_r(i,o,n)=\frac{F(i,h_r)G(i,o,h_r)D(h_r)}{4|i \cdot n||o \cdot n|}
$$

이 모델은 Cook-Torrance BSDF와 같다. 분모에 $\pi$대신에 $4$로 변환된것만 다르다. 초기 논문에는 $D$항에 대한 정규화가 달랐지만 지금은 상수 $4$를 쓰는것으로 합의되었고 일반화 된 상황이다.

## 참고문헌
[M. Pharr etal] Physically Based rendering from Theory to Implementation Third Edition\
[B. Walter etal] Microfacet Models for Refraction through Rough Surfaces, Eurographics Symposium on Rendering, 2007\
[E. Heitz] Understanding the Masking-Shadowing Function  Microfacet-Based BRDFs, Journal of Computer Graphics Techniques Vol. 3, No. 2, 2014
