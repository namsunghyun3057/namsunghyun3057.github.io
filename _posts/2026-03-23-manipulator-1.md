---
title: Manipulator Study 1
date: 2026-03-23 21:52:00 +0900
categories: [Physical AI, Manipulator]
tags: [robotics, manipulator, kinematics]
---

# [Manipulator] 돌밭 매니퓰레이터 스터디 1주차  
  
로봇 제어 및 물리적 AI 구현을 위한 필수 기초인 '기구학(Kinematics)' 및 제어 요소 정리  
매니퓰레이터(로봇팔)의 전체 구조, 좌표계 변환, 하드웨어 설계 기초와 실제 모터 제어(PID)의 핵심

---

## 1. 매니퓰레이터 전체 구조 이해

매니퓰레이터란?  
> 인간의 팔을 모방한 로봇 기구학적 모델!

### Link, Joint, DOF(자유도)
로봇 팔을 구성하는 핵심 물리적 요소.
* **Link (링크):** 로봇의 형태를 유지하는 단단한 뼈대(구조물).
* **Joint (조인트):** 링크 간 연결 및 실제 움직임(회전형 Revolute, 병진형 Prismatic)을 발생시키는 관절.
* **DOF (Degrees of Freedom, 자유도):** 3차원 공간에서 독립적으로 움직일 수 있는 방향의 수. 보통 조인트 모터의 개수와 일치함. 3차원 위치(X,Y,Z)와 자세(3축 회전)를 완벽히 제어하려면 최소 6자유도가 필요함.

<div align="center">
  <img src="/assets/img/1-1.png" alt="1-1" style="width: 80%; max-width: 600px; display: block; margin: 0 auto;">
  <p><em>매니퓰레이터의 Link와 Joint</em></p>
</div>


### Workspace (작업 영역)
로봇팔의 끝단(End-effector)이 물리적으로 도달할 수 있는 3차원 공간의 집합.
* **Reachable Workspace:** 단순히 끝이 닿을 수 있는 전체 영역.
* **Dexterous Workspace:** 닿은 상태에서 모든 방향으로 자유롭게 자세 전환이 가능한 핵심 영역.

<div align="center">
  <img src="/assets/img/1-2.png" alt="1-2" style="width: 80%; max-width: 600px; display: block; margin: 0 auto;">
  <p><em>매니퓰레이터의 Workspace</em></p>
</div>


---

## 2. 공간 인식과 좌표계 기초

로봇은 시각적 직관이 아닌, 수학적 '좌표 변환'을 통해 위치와 자세를 계산한다!

### 1) Rotational Transformations 
* 베이스(어깨) 좌표계 기준, 로봇 손끝의 회전 상태를 3 × 3 회전 행렬(Rotation Matrix)로 표현.
* 각 관절의 회전 변환 행렬을 누적 곱셈하여 최종 끝단의 자세를 도출함.

### 2) Rotation with Respect to the Current Frame
회전 기준축에 따른 행렬 곱셈 순서의 차이.
* **Fixed Frame (고정 좌표계) 기준:** 변하지 않는 외부 축 기준 시, 회전 행렬을 **앞에 곱함 (Pre-multiplication)**.
* **Current Frame (현재 좌표계) 기준:** 방금 움직여 형성된 현재의 축 기준 시, 회전 행렬을 **뒤에 곱함 (Post-multiplication)**.

### 3) Euler Angles 
* 복잡한 행렬 대신, 3개의 직교 축을 순차적으로 회전시켜 직관적으로 자세를 표현하는 방법.
* 항공, 모빌리티 등에서 주로 사용하는 **Roll(앞뒤 축), Pitch(좌우 축), Yaw(수직 축)** 회전이 대표적임.

<div align="center">
  <img src="/assets/img/1-3.png" alt="1-3" style="width: 80%; max-width: 600px; display: block; margin: 0 auto;">
  <p><em>Roll, Pitch, Yaw</em></p>
</div>


---

## 3. 매니퓰레이터 설계 기초

기구학적 계산을 실제 물리적 하드웨어로 구현할 때 고려해야 할 핵심 요소.

* **가반하중 (Payload):** 로봇 끝단에서 안정적으로 들어 올릴 수 있는 최대 무게. (모터 토크 및 링크 강성 결정)
* **작업 반경 (Reach):** 목적에 맞는 링크 길이 및 최소 요구 DOF 설정.
* **정밀도 및 반복성:** 목표 좌표 도달의 정확도, 동일 작업 반복 시 발생하는 오차율 최소화.
* **질량 관성 (Inertia):** 급가속/급정지 시의 제어 용이성을 위해 튼튼하고 가벼운 소재 적용 필요.
