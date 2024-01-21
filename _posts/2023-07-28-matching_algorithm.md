---
layout: post
title: "[Node.js] 90줄 시간표 매칭 알고리즘"
categories: [dev]
---

## 🛒 시간표 매칭 알고리즘

오늘 90줄짜리 매칭 알고리즘을 짰다..

teachers, students는 선생님과 수강생들의 이름을 담아 두었다.

tValueList, sValueList는 선생님과 수강생들의 되는 시간 리스트를 담은 리스트이다.

예를 들면

```
tValueList = [
    [
        ['양말', '월', '10:00', '15:00'],
        ['양말', '화', '10:00', '15:00'],
        ['양말', '금', '19:00', '20:30']
    ],
    [
        ['노드', '일', '09:00', '15:30'],
        ['노드', '월', '10:00', '19:00'],
        ['노드', '화', '10:00', '15:00'],
        ['노드', '금', '19:00', '20:30']
    ],
    [
        ['자바', '일', '09:00', '15:30'],
        ['자바', '화', '10:00', '12:00'],
        ['자바', '화', '14:00', '21:00'],
        ['자바', '금', '16:00', '20:30']
        ['자바', '토', '15:00', '22:30']
    ]
]
```

그리고 안쪽 리스트의 길이만큼 정렬이 되어 있다. 되는 시간이 제일 적은 사람을 먼저 매칭시켜야 하기 때문이다.

다 짜고 보니 tIdx, taIdx.. 이런 것들은 외부에 선언하지 않아도 되었을 것 같다.

완성된 매칭 정보는 okList에 들어간다.

**아니.. 아냐 사실 이거 오류가 있다 나중에 수정할 것**

```js
let okList = []; //매칭 정보
let readyQ = []; //매칭 대기열
teachers.forEach(el => readyQ.push(el.t_id));
students.forEach(el => readyQ.push(el.s_id));

//매칭 시작, 선생님 리스트 끝까지 갈 때까지 무한 반복
let tIdx = 0; //선생님 리스트 인덱싱
let taIdx = 0; //선생님A의 되는 시간 리스트 인덱싱
let sIdx = 0; //수강생 리스트 인덱싱
let saIdx = 0; //수강생B의 되는 시간 리스트 인덱싱
while(true) {
    for(tIdx = 0; tIdx < tValueList.length; tIdx++) {
        //선생님 한 명의 되는 시간 리스트를 가져옴
        let aTeacherScheduls = tValueList[tIdx];
        let tId = aTeacherScheduls[0].tt_tid; //이 리스트의 선생님 이름
        let isMatchedStudent = false; //수강생 끝까지 매칭이 되었는지 확인하는 변수
        if(readyQ.indexOf(tId) !== -1) {
            //선생님이 매칭되지 않았을 때만 매칭 시작
            for(taIdx = 0; taIdx < aTeacherScheduls.length; taIdx++) {
                //선생님 한 명의 되는 시간 한 개를 가져옴
                let aTSchedule = aTeacherScheduls[taIdx];
                //수강생 중 맞는 사람이 있는지 확인
                for(sIdx = 0; sIdx < sValueList.length; sIdx++) {
                    let aStudentSchedules = sValueList[sIdx];
                    let sId = aStudentSchedules[0].st_sid;
                    if(readyQ.indexOf(sId) !== -1) {
                        for(saIdx = 0; saIdx < aStudentSchedules.length; saIdx++) {
                            let aSSchedule = aStudentSchedules[saIdx];
                            //aTSchedule과 aSSchedule이 맞는 일정인지 확인
                            if((hourToMin(aTSchedule.tt_start) > hourToMin(aSSchedule.st_end)
                            || hourToMin(aTSchedule.tt_end) < hourToMin(aSSchedule.st_start))){
                                console.log(aTSchedule.tt_start);
                            }
                            else {
                                let resultStartTime = '';
                                let resultEndTime = '';
                                //기준이 될 수 있는 시작 시간과 종료 시간을 구함
                                //1. 선생님 시작 시간보다 수강생 시작 시간이 늦을 때
                                if(hourToMin(aTSchedule.tt_start) < hourToMin(aSSchedule.st_start)) {
                                    //무조건 학생 시작시간 기준
                                    resultStartTime = aSSchedule.st_start;

                                    //종료시간을 알아보아야 함
                                    //2. 선생님 끝나는 시간이 수강생 끝나는 시간보다 늦을 때
                                    //=> 선생님 가능 시간이 수강생 가능 시간을 감쌀 때
                                    if(hourToMin(aTSchedule.tt_end) > hourToMin(aSSchedule.st_end)) {
                                        resultEndTime = aSSchedule.st_end;
                                    } else {
                                        //3. 수강생 끝나는 시간이 선생님 끝나는 시간보다 늦을 때
                                        resultEndTime = aTSchedule.tt_end;
                                    }
                                } else {
                                    //3. 수강생 시작시간보다 선생님 시작시간이 늦을 때
                                    //무조건 선생님 시작시간 기준
                                    resultStartTime = aTSchedule.tt_start;

                                    //종료시간을 알아보아야 함
                                    //4. 선생님 끝나는 시간이 수강생 끝나는 시간보다 늦을 때
                                    if(hourToMin(aTSchedule.tt_end) > hourToMin(aSSchedule.st_end)) {
                                        resultEndTime = aSSchedule.st_end;
                                    } else {
                                        //5. 수강생 끝나는 시간이 선생님 끝나는 시간보다 늦을 때
                                        //=> 수강생 가능 시간이 선생님 가능 시간을 감쌀 때
                                        resultEndTime = aTSchedule.tt_end;
                                    }

                                }
                                //매칭이 가능한 경우, 120시간 이상인지 확인
                                if(hourToMin(resultEndTime) - hourToMin(resultStartTime) >= 120) {
                                    //120시간 이상인 경우, readyQ에서 선생님과 수강생을 제거, okList에 매칭 정보 저장
                                    readyQ.splice(readyQ.indexOf(tId),1);
                                    readyQ.splice(readyQ.indexOf(sId),1);
                                    okList.push([tId, sId, resultStartTime, resultEndTime]);
                                    console.log(readyQ);
                                    console.log(okList);
                                    isMatchedStudent = true;
                                    break;
                                }
                            }
                        }
                    }
                    //이 수강생에 대한 매칭이 되었다면 다음 선생님으로 넘어감
                    if(isMatchedStudent) break;
                }
                //이 수강생에 대한 매칭이 되었다면 다음 선생님으로 넘어감
                if(isMatchedStudent) break;
            }
        }
    }
    //readyQ가 모두 비어 모든 사람들이 매칭된 경우 while true를 종료
    if(readyQ.length === 0) break;
}
```