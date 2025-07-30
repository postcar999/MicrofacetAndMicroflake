# Understanding of Microfacet And Microflake

Microfacet에서 BRDF를 정의할때, 입사방향 $\omega_i$과 방사방향 $\omega_o$, 그리고 macrosurface 노멀 $n$ micfosurface 노멀$m$

In microfacet models, a detailed microsurface is replaced by a simplified macrosurface with a modified scattering function (BSDF) that matches the aggregate directional scattering of the microsurface. This assumes that micro surface detail is too small to be seen directly, so only the far-field directional scattering pattern matters. {B. Walter et al]

## Microfacet 이론
BSDF는 면에 빛이 어떻게 산란이 되는지를 묘사하는 함수이다. 다시 설명하자면, 입사방향$i$의 irradiance와 방사방향$o$의 radiance의 비율을 정의한다. 이 관계를 수식으로 함수로 표현하면 $f_s(i,o,n)$이다. 반사만 구성된다면 BRDF, 투과의 경우 BTDF로 말한다. 여기서는 반사만 이해하고 설명하겠다.

### Macrosurface BSDF Integral
Macrosurface BSDF모델은 미시적관점에서 이뤄지는 산란의 합 들로 구성한다. 즉 Microsurface단계에서 보이는 모든 시각적인 영향을 단일모델로 표현가능하게 총합(적분)하여 계산하게 한다. 미세면들의 노멀을 $m$으로 정의하고 시각적으로 전달되는 모든 면들을 D항 G항의 곱으로 표현한다. 정확한 요소를 적용하기 위해 미세면들로 입사하는 irradiance량을 microsurface로 변환하고 산란된 radiance들을 다시 macrosurface로 변환하려 돌려준다.

$f_s(i,o,n) = \int |\frac{i \cdot m}{i \cdot n}| f_s^m(i,o,m) |\frac{o \cdot m}{o \cdot n}|G(i,o,m)D(m)d\omega_m$ [Eq. 1]

위 수식의 의미를 풀어쓴다면,

$f_s(i,o,n)=\int(i를\,m으로\,변환)\cdot(m의\,반사와\,투과함수)\cdot(o를\,m으로\,변환)\cdot G \cdot D \cdot 미소방향m$

$f_s^m(i,o,m)$은 이상적인(ideal) 반사와 투과 함수로 정의한다.

## Microsurface Specualr BSDFs
다시한번 언급하면 반반한(flat) 거울면들의 집합체로 미세면들을 정의하고, 이 면들을 거시적 관점으로 전환하여 모델화 시킨것이 Microfacet 분포 모델이라고 할 수 있다. 입사하는 에너지량($\rho$)이 입사방향($i$)에서 단일 스페큘러 반사 방향($o$)일때, 스페큘러 BSDF를 다음과 정의한다.
$f_s^m(i,o,m)=\rho\frac{\delta_{\omega_o}(s, o)}{|o \cdot m|}$

$o$방향 입체각(solid-angle)을 측정량을 $d\omega_{o}$로 표현하고, 이 측정량으로 적분을 하면 다음 과 같이 된다:
$\int_{\Omega}g(o)\delta_{\omega_{o}}(s,o)d\omega_{o}=\{g(s)\quad if \in\Omega, 0\quad otherwise\}$

[Eq.1]에서 위BSDF를 사용하려면, microsurface의 노멀과 그에 맞는 입체각 변환을 도입해야한다.
먼저 입사방향과 방사방향이 주어진 상황에서 단일 microsurface노멀을 계산 할 수 있다. $i$에서 $o$로 전환될때 에너지가 산란되고, 여기서 미시적으로 노멀 역활할 microsurface노멀을 정의한다. 이 노멀은 half-vector이다. 그다음 $h$와 $m$사이의 델타 함수로 다시 BSDF를 만든다:

$f_s^m(i,o,m)=\rho(i,m)\frac{\delta_{\omega_{o}}(h(i,o),m)}{o \cdot m}||\frac{\partial\omega_{h}}{\partial\omega_{o}}||$

여기서 $||\frac{\partial\omega_{h}}{\partial\omega_{o}}||$는 Jocobian 행렬식 절대값이다. 이 Jacobian행렬은 $h$와 $o$간의 변환을 한다. 이 Jocobian은 두 공간의 편차 크기를 말한다. 방향$o$의 미소 입체각$d\omega_{o}$만큼 변화 했을때 $h$방향의 미소 입체각$d\omega_{o}$의 변화를 말한다:
$||\frac{\partial\omega_{h}}{\partial\omega_{o}}||=\lim_{d\omega_{o} \to 0}\,\frac{d\omega_{h}}{d\omega_{o}}$

입체각은 구면의 투영된 면적에 직접적인 연관을 말한다. 그리고 이 극미량 면적은 평면에 가깝다고 볼 수 있다. 이 극소 입체각$d\omega_{o}$이 극소 입체각$d\omega_{h}$로 변환되는 과정을 기하학적으로 풀어내면 다음과 같다:
$||\frac{\partial\omega_{h}}{\partial\omega_{o}}||=\frac{|o \cdot h_{r}|}{||\vec{h_{r}}||^2}$ [Eq. 2]

### $f_r^m$ 이상적 반사(Ideal Reflection)
[Eq.2]에서 좀더 풀어나가면 분모는 $o$와$h$간의 반사 벡터의 2제곱으로 다시 풀어쓸 수 있으며,
$\frac{|o \cdot h_{r}|}{||\vec{h_{r}}||^2}=\frac{|o \cdot h_{r}|}{(2|o \cdot h_{r}|)^2}=\frac{1}{4|o \cdot h_{r}|}$ [Eq.3]

[Eq.3]식에서는 $||\vec{h_r}||=(\vec{h_r} \cdot h_{r})$ 그리고 $(o \cdot h_r)=(i \cdot h_r)$를 사용한다. 그리고 $\rho$는 프레넬 계수 $F$와 같으므로 microsurface BSDF는,
$f_r^m(i,o,m)=F(i,m)\frac{\delta_{\omega_m}(h_r,m)}{4(i \cdot h_r)^2}$

여기서 Jacobian행렬 도입을 다른 의미로 설명해 보자면, $f_r^m$이 증가하려면$|i \cdot h_r|$이 감소 해야한다. 즉 이 상황은 off specular(정반사외 반사영역)를 연출하는 효과가 나온다.

### $f_t^m$, 이상적 투과(Ideal Refraction)
본 문서에서는 투과부분은 설명하지 않는다. 그래서 $f_s^m(i,o,m)=f_r^m(i,o,m)$이라고 정의하고 넘어간다.

## 거친 표면을 위한 BSDF