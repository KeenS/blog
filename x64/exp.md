# float�̎w���֐���AVX-512�ɂ�����
�w���֐�exp()�͐[�w�w�K�̊������֐���tanh��V�O���C�h�֐��Ȃǂ𗘗p����Ƃ��Ɏg���܂��B
exp�̋ߎ��v�Z��AVX-512���g�������@���Љ�܂��B

## ��������֐�
```
void expf_vC(float *dst, const float *src, size_t n)
{
    for (size_t i = 0; i < n; i++) {
        dst[i] = std::exp(src[i]);
    }
}
```

## �R�[�h
[fmath2.hpp](https://github.com/herumi/fmath/blob/master/fmath2.hpp)�ɂ���܂��B

```
fmath::void expf_v(float *dst, const float *src, size_t n);
```
�Ƃ����֐�����`����Ă��āA��Lexpf_vC��(�덷��������)�����̏��������܂��B

## �x���`�}�[�N

### ���x
�x���`�}�[�N��[exp_v.cpp](https://github.com/herumi/fmath/blob/master/exp_v.cpp)�ōs���܂����B

`float x[16384];`�ɑ΂���exp�����߂�v�Z��expf_vC��expf_v�ƂŔ�r���܂����B

����OS : Ubuntu 19.10, CPU : Xeon Platinum 8280 2.7GHz, compiler : gcc-9.2.1 -Ofast�ł��B

�֐�|std::exp|expf_v
:-|:-|:-
����(clk)|140.8k|10.6k

clk��rdtsc�ɂ��CPU�N���b�N�̌v����10�{�ȏ㍂��������Ă��܂��B

### �덷

���Ό덷��(�^�̒l - �����l) / �^�̒l�Ƃ��܂��B
`std::exp(float x)`��^�̒l�Ƃ���x = -30����30�܂�1e-5�����₵�Čv�Z�����l�̑��Ό덷�̕��ς��o����2e-6�ƂȂ�܂����B

## �A���S���Y��

### exp(x)�̐���
�w���֐�exp(x)�͐��w�I�ɂ�

exp(x) = 1 + x + x^2/2! + x^3/3! + ... + x^n/n! + ...

�ƒ�`����Ă��܂��B
x�̓}�C�i�X�����傩��v���X������܂łƂ肦�܂��B

������`!`�͊K��̋L����`4! = 4 * 3 * 2 * 1`�ł��B
��`�͖����̒l�̘a�ł����A�R���s���[�^�ł͂������r���őł��؂��ėL���a�ŋߎ��v�Z���܂��B

x�̐�Βl��1��菬�����Ƃ���x^n�͂ƂĂ��������A�X��n!�Ŋ���̂ł����Ə������Ȃ�܂��B
���������ď��Ȃ��a�őł��؂��Ă��덷�͏������Ă��݂܂��B
������x��1���傫����x^n�͂ƂĂ��傫���Ȃ�A�ł��؂�덷���傫���Ȃ�܂��B
�ǂ̂悤�ɂ��Č덷�����������邩���|�C���g�ł��B

y = a^x�̋t�֐���x = log_a(y)�Ə����܂��B
����a = e�̂Ƃ�log_e(y) = log(y)�Əȗ����܂��B

- a^(x+y) = a^x a^y
- log(x^y) = y log(x)
- x^y = z^(y log_z(x)) ; ��̕ϊ�����

�Ȃǂ����藧���܂��B

### �v�Z�͈̔�

�܂�x�̂Ƃ蓾��͈͂𒲂ׂ܂��傤�B
x�̌^��float�ł�(�����ł�x64���ΏۂȂ̂�float��32bit���������_���Ƃ��܂�)�B
���ׂĂ݂��x = -87.3��菬������float�Ő�����������ŏ��̐�FLT_MIN=1.17e-38��菬�����A
�t��x = 88.72���傫���ƍő�̐�FLT_MAX=3.4e38�����傫���Ȃ���inf�ɂȂ�܂��B
�]����x��-87.3 <= x <= 88.72�Ƃ��Ă悢�ł��傤�B
  - �������S�̂��߂ɃN���b�s���O�����Ă��܂������ʏ�̃f�[�^�������ꍇ�͍폜���Ă���肠��܂���B

### �ߎ��v�Z
2�̐����Џ�2^n�̓r�b�g�V�t�g���g���č����Ɍv�Z�ł��܂��B
����������exp(x) = e^x����2�̐����Ђ����o�����Ƃ��l���܂��B

��̕ϊ��������g����

e^x = 2^(x log_2(e))

�ƕό`���Ax'=x log_2(e)�𐮐�����n�Ə�������a�ɕ������܂��B

x' = n + a (|a| <= 0.5)

���������e^x = 2^n �~ 2^a�ł��B

2^n�̓r�b�g�V�t�g�Ōv�Z�ł���̂Ŏc���2^a�̌v�Z�ł��B
�����ōēx��̕ϊ������܂��B

2^a = e^(a log(2)) = e^b ; b = a log(2)�Ƃ���

|a| <= 0.5��log(2) = 0.693�Ȃ̂�|b| = |a log(2)| <= 0.346.

b��0�ɋ߂��l�Ȃ̂�e^b��`���̋����W�J���g���ċߎ��v�Z���܂��B

6���̍���0.346^6/6! = 2.4e-6��float�̕���\�ɋ߂��̂�5���Ő؂�܂��傤�B

e^b = 1 + b + b^2/2! + b^3/3! + b^4/4! + b^5/5!

### �A���S���Y��
���ǎ��̃A���S���Y�����̗p���܂��B

```
input : x
output : e^x

1. // x = max(min(x, expMax), expMin)
2. x = x * log_2(e)
3. n = round(x) ; �l�̌ܓ�
4. a = x - n
5. b = a * log(2)
6. z = 1 + b(1 + b(1/2! + b(1/3! + b(1/4! + b/5!))))
7. return z * 2^n
```

�ӊO�ƒZ���ł��ˁB

## AVX-512�ł̎���

AVX-512�͈�̃��W�X�^��512�r�b�g����̂�float�Ȃ�512/32 = 16�܂Ƃ߂ď����ł��܂��B
���W�X�^��zmm0����zmm31�܂�32���p�ł��܂��B

### AVX-512�̖��ߊT��
AVX-512�p�̖��߂��܂Ƃ߂Ă����܂��B
�ڍׂ�[Intel64 and IA-32 Architectures Software Developer Manuals](https://software.intel.com/en-us/articles/intel-sdm)���Q�Ƃ��������B

�A�Z���u������̕\�L��Intel�`���ŁA�I�y�����h��dst, src1, src2�̏����ł��B
dst, src��dst, dst, src�̏ȗ��L�@�ł��B


����|�Ӗ�|����|
-|-|-|
vmovaps [mem], zmm0<br>vmovaps zmm0, [mem]|[mem] = zmm0<br>zmm0 = [mem]|mem��16�̔{���̃������A�h���X�ł��邱��|
vmovups [mem], zmm0<br>vmovaps zmm0, [mem]|[mem] = zmm0<br>zmm0 = [mem]|mem�̐���͂Ȃ�|
vaddps zmm0, zmm1, zmm2|zmm0 = zmm1 + zmm2|float�Ƃ���<br>vsubps, vmulps�Ȃǂ����l|
vminps zmm0, zmm1, zmm2|zmm0 = min(zmm1, zmm2)|float�Ƃ���|
vfmadd213ps zmm0, zmm1, zmm2|zmm0 = zmm0 * zmm1 + zmm2|float�Ƃ���|
vpbroadcastd zmm0, eax|eax��16��zmm0�ɃR�s�[����||
vrndscaleps zmm0, zmm1, 0|zmm0 = round(zmm1)|���ʂ�float�^|
vscalefps zmm0, zmm1, zmm2|zmm0 = zmm1 * 2^zmm2|zmm2��float�^��floor����|

### ������

```cpp
// exp_v(float *dst, const float *src, size_t n);
void genExp(const Xbyak::Label& expDataL)
{
#ifdef XBYAK64_WIN
    const int keepRegN = 6;
#else
    const int keepRegN = 0;
#endif
    using namespace Xbyak;
    util::StackFrame sf(this, 3, util::UseRCX, 64 * keepRegN);
```

StackFrame�͊֐��̃v�����[�O�𐶐�����N���X�ł��B
3�͈�����3�AUseRCX��rcx���W�X�^�𖾎��I�Ɏg���w��A
zmm���W�X�^�̕ۑ��̂���64 * keepRegN byte�X�^�b�N���m�ۂ��܂��B


```cpp
    const Reg64& dst = sf.p[0];
    const Reg64& src = sf.p[1];
    const Reg64& n = sf.p[2];
```

StackFrame�N���X��sf.p[i]�Ŋ֐��̈�����i�Ԗڂ̃��W�X�^��\���܂��B
Windows��Linux�Ƃň����̃��W�X�^���قȂ�̂ł����ŋz�����܂��B

```
    // prolog
#ifdef XBYAK64_WIN
    for (int i = 0; i < keepRegN; i++) {
        vmovups(ptr[rsp + 64 * i], Zmm(i + 6));
    }
#endif
```
AVX-512��Zmm���W�X�^��ۑ����܂��B
�֐�����Windows�ł�zmm6�ȍ~�𗘗p����ꍇ�͕ۑ�����K�v������܂��B

```
    // setup constant
    const Zmm& expMin = zmm3;
    const Zmm& expMax = zmm4;
    const Zmm& log2 = zmm5;
    const Zmm& log2_e = zmm6;
    const Zmm expCoeff[] = { zmm7, zmm8, zmm9, zmm10, zmm11 };
    lea(rax, ptr[rip+constVarL]);
    vbroadcastss(expMin, ptr[rax + offsetof(ConstVar, expMin)]);
    ...
```
�e��萔�����W�X�^�ɃZ�b�g���܂��B
vpbroadcastd��float�ϐ�1��32��Zmm���W�X�^�ɃR�s�[���閽�߂ł��B

```cpp
    vpbroadcastd(expMin, ptr[rip + expDataL + (int)offsetof(ConstVar, expMin)]);
```

��Xbyak���L�̏������ł��B
Label�N���XexpDataL�͊e��萔(ConstVar�N���X)���u����Ă���擪�A�h���X���w���܂��B
rip�ő��΃A�h���X�𗘗p���AC��offsetof�}�N���ŃN���X�����o�̃I�t�Z�b�g�l�����Z���܂��B

### ���C���R�[�h

```
vmulps(zm0, log2_e); // x *= log_2(e)
vrndscaleps(zm1, zm0, 0); // n = round(x)
vsubps(zm0, zm1); // a = x - n
vmulps(zm0, log2); // a *= log2
```

�A���S���Y����1����5�s�ڂɑΉ����܂��B
vmulps��log_2(e)�{���܂��B
vrndscaleps�Ő����֍ŋߎ��ۂ�(round)���܂��B��3������0���ŋߎ��ۂ߂�\���܂��B
���ʂ�float�^�ł��B

```cpp
vmovaps(zm2, expCoeff[4]); // 1/5!
vfmadd213ps(zm2, zm0, expCoeff[3]); // b * (1/5!) + 1/4!
vfmadd213ps(zm2, zm0, expCoeff[2]); // b(b/5! + 1/4!) + 1/3!
vfmadd213ps(zm2, zm0, expCoeff[1]); // b(b(b/5! + 1/4!) + 1/3!) + 1/2!
vfmadd213ps(zm2, zm0, expCoeff[0]); // b(b(b(b/5! + 1/4!) + 1/3!) + 1/2!) + 1
vfmadd213ps(zm2, zm0, expCoeff[0]); // b(b(b(b(b/5! + 1/4!) + 1/3!) + 1/2!) + 1) + 1
```
�A���S���Y����6�s�ڂɑΉ����܂��B
vfmadd213ps(x, y, z)�͐Ϙa���Z���߂ŁAx = x * y + z�����s���܂��B

```
vscalefps(zm0, zm2, zm1); // zm2 * 2^zm1
```
`vscalefps`��AVX-512��p���߂ł��B
��L��`vrndscaleps`�ŋ��߂�n��2�ׂ̂�����|���܂��B
�����exp(x)�̌v�Z���I���ł��B

## 2�Ђ�vscalefps
AVX2�܂ł͐���n�ɑ΂���float(2^n)�͎��̂悤�ɋ��߂Ă��܂����B

float�͕����r�b�gs�A�w����e�A������f����Ȃ�܂��B
���ꂼ��s��1�r�b�g�Ae��8�r�b�g�Af��23�r�b�g�ō��v32�r�b�g�ł��B

����|����|�w����|������
-|-|-|-|
�L��|s|e|f|
�r�b�g��|1|8|23|

���������ăr�b�g�p�^�[����`[s:e:f]`�ŕ\�����float��`(-1)^s �~ 2^(e-127) �~ (1 + f/2^23)`�Ƃ����l��\���܂��B
�����float(2^n)�����߂�ɂ�`vrndscaleps`�̌��ʂ𐮐��^�ɃL���X�g����n�����߂���ŁA

```
((n + 127) << 23)
```
�Ƃ���32�r�b�g�����̃f�[�^�𐶐����A�����float�^�ƌ��Ȃ��ď�Z����K�v������܂����B
`vscalefps`�͂����1���߂Ŏ��s�ł��܂��B
127�Ƃ����萔�l��SIMD���W�X�^�ɑ������K�v���Ȃ��Ȃ�Afloat��int�̌^�ϊ��������Ȃ�̂ŕ֗��ō����ł��B

## �[������

float��16����������ƌ��̔z��̌�n��16�̔{���łȂ��Ƃ��[�����o�܂��B
���̏������@�ɂ��ĉ�����܂��B

AVX2�܂ł�SIMD���߂ł͒[�����������ł����B
���߂�16�P�ʂȂ̂Ŏc��5�����W�X�^�ɓǂݍ��ނƂ��������������ɂ����̂ł��B
���̂���SIMD���߂��g��Ȃ��ʏ�̕��@�Ń��[�v���񂷕��@���Ƃ邱�Ƃ������ł��B

AVX-512�ł͂�����������邽�߂̃}�X�N���W�X�^k1, ..., k7���o�ꂵ�Ă��܂��B
�}�X�N���W�X�^�͊e�r�b�g���f�[�^��������(1)�����Ȃ�(0)�����w�肷�郌�W�X�^�ł��B
�f�[�^�������Ȃ��ꍇ�͍X�Ƀ[���Ŗ��߂�(T_z)���l��ύX���Ȃ�����I���ł��܂��B

���Ƃ���

```
vmovups(zmm0|k1|T_z, ptr[src]); // zmm0 = *src;
```
��k1 = 0b11111;�̏ꍇ�A����5�r�b�g�������Ă���̂�float *src��src[0], ..., src[4]������zmm0�ɃR�s�[����AT_z���w�肵�Ă���̂Ŏc��̓[���Ŗ��߂��܂��B

�����R�[�h�ł̉���ɖ߂�܂��B

```cpp
and_(ecx, 15); // ecx = n % 16
mov(eax, 1); // eax = 1
shl(eax, cl);  // eax = 1 << n
sub(eax, 1);   // eax = (1 << n) - 1 ; n�r�b�g��mask
kmovd(k1, eax); // �}�X�N�ݒ�
```
ecx�Ƀ��[�v�̉�n�������Ă���Ƃ�15��and���Ƃ�[���𓾂܂��B
((1 << n) - 1)�̓f�[�^�������Ă��Ȃ�������0�ƂȂ�}�X�N�ł�(n = 3�Ȃ�0b111�ƂȂ�)�B

```cpp
vmovups(zm0|k1|T_z, ptr[src]);
```
`vmovups(zm0, ptr[src])`��src����zm0��16��float��ǂޖ��߂ł����A
`vmovups(zm0|k1|T_z, ptr[src])`��k1�Ń}�X�N����Ǝw�肵���r�b�g�����������������������ɃA�N�Z�X���܂���B

�����ŏd�v�ȓ_�͓����I��512�r�b�g�ǂݍ���ł���[���ɂ���̂ł͂Ȃ�
�}�X�N����Ă��Ȃ��̈��read/write�����������Ă���O���������Ȃ��Ƃ����_�ł��B

���S���ăy�[�W���E�ɃA�N�Z�X�ł��܂��B

## �W���̌��ߕ�

�Ō�ɃA���S���Y����6�s��

```
6. z = 1 + b(1 + b(1/2! + b(1/3! + b(1/4! + b/5!))))
```

�̒l�̉��P���@�ɂ��ďЉ�܂��B
���̎��͖����ɑ����a��r���őł��؂������̂ł����B
���������ĕK���������l�����������Ȃ�܂��B

1/k!�̒l����������邱�ƂŌ덷����菬�����o���܂��B

b�̂Ƃ蓾��͈͂�L = log(2)/2�Ƃ���[-L, L]�ł����B
�֐�f(x) = 1 + A + Bx + Cx^2 + Dx^3 + Ex^4 + Fx^5�Ƃ���
���[-L, L]��f(x)��exp(x)�̍���2��덷�̕��ς��ŏ�������(A, B, C, D, E, F)�������܂��B

���w�I�ɂ�I(A, B, C, D, E, F):=��_\[-L,L\](exp(x) - f(x))^2 dx�Ƃ���
I��A, B, C, D, E, F�ŕΔ��������l���S��0�ɂȂ�������߂܂��B

Maple�ł�

```maple
f := x->A+B*x+C*x^2+D*x^3+E*x^4+F*x^5;
g:=int((f(x)-exp(x))^2,x=-L..L);
sols:=solve({diff(g,A)=0,diff(g,B)=0,diff(g,C)=0,diff(g,D)=0,diff(g,E)=0,diff(g,F)=0},{A,B,C,D,E,F});
Digits:=1000;
s:=eval(sols,L=log(2)/2);
evalf(s,20);
```

�ŋ��߂܂����B
�G�Ȕ�r�ł����P���ɑł��؂����Ƃ��ɔ�ׂČ덷���������x�ɂȂ�܂����B

[Sollya](http://sollya.gforge.inria.fr/)���g����

```
remez(exp(x),5,[-log(2)/2,log(2)/2]);
```
�ŋ��߂����������܂�(�l�I�ɂ�2��덷������������O�҂̕����悢��� : ���l�v�Z���̕������Ă�������)�B
