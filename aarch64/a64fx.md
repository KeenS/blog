# A64FX�̃A�[�L�e�N�`��

## �}�j���A��

[A64FX Microarchitecture Manual](https://github.com/fujitsu/A64FX)

## �X�e�[�W

A64FX�͖��߂��X�[�p�[�X�J���[�Ŏ��s����B
- �����ɕ����̖��߂����s�ł���B
- �ˑ��֌W�̂Ȃ����߂̏�������ꊷ����(�A�E�g�I�u�I�[�_�[)�B
- �����𕡐��̗v�f�ɕ������āA���s�ɏ���������(�p�C�v���C��)�B

�傫��5�̃X�e�[�W����Ȃ�
- ���߃t�F�b�`�X�e�[�W
  - �������A���߃L���b�V���╪��\���Ȃǂ�p���Ė��߂�ǂݍ���IBUFF(Instruction BUFFer)�ɕۑ�����
- �f�R�[�h�X�e�[�W
  - IBUFF����ő�4���ߎ擾���ă�OP���߂ɕ�������
  - movprfx�͌㑱���閽�߂�pack���������
  - �f�R�[�h���ꂽ���߂����U�x�[�V�����E�X�e�[�V����RS(Reservation Station)�ɓn��
- ���s�X�e�[�W
  - RS�̃�OP�����s����
    - �������Z(EXA/EXB)
    - SIMD&FP, SVE(FLA/FLB)
    - �q�ꖽ��(PR)
    - ���[�h�E�X�g�A(EAGA/EAGB)
    - ���򖽗߃p�C�v���C��
- ���[�h�E�X�g�A�X�e�[�W
  - �f�[�^�L���b�V���⃍�[�h�E�X�g�A�p�C�v���C��
- �R�~�b�g�X�e�[�W
  - ���s���ʂ̊m�F������
    - ��O�╪��\�����ʂ̊m�F

�� SVE�̉��Z�̃p�C�v���C���̗�

-|1,2|3�`6|7,8|9�`17|18�`20|21|22|23,34
-|-|-|-|-|-|-|-|-
SVE|D|P|B|X|U|EXP|C|W

- D ; �f�R�[�h
- P ; �X�P�W���[�����O
- B ; �������W�X�^�̓ǂݍ���
- X ; ���ߎ��s
- U ; ���ʂ̍X�V
- EXP ; ��O����
- C ; �R�~�b�g
- W ; �������W�X�^�̍X�V

## ���C�e���V
- ��L���ߎ��s�ɂ����鎞��(�N���b�N��)
- �����̖��߂̃^�C�~���O�ɂ���ē����X�e�[�W�ɂԂ���ƃ��C�e���V��؂芷����(���΂���)�Ή�����
- ���߂ɂ���ă��C�e���V�͈قȂ�

��Ȗ��߂̃��C�e���V
- add, sub ; 4
- �_�����Z ; 3~4
- fadd, fmul, fmad, fscale ; 9
- whilelt ; 1+3
- incd ; 4
- ld1w ; 11
- frinta, frinti, frintm, frintn�Ȃ� ; 9
- fmax, fmin ; 4
- fsqrt ; 98(float x 16�̂Ƃ�)
- fdiv ; 98(float x 16�̂Ƃ�)
- fexpa, frecpe, frsqrte ; 4
- frecps, frsqrts ; 9
