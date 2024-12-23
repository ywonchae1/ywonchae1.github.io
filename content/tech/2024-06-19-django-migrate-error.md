---
title: "Django migrations error를 올바르게 해결하기"
date: 2024-06-19
tags:
  - Django
authors:
  - name: ywonchae1
    link: https://github.com/ywonchae1
    image: https://github.com/ywonchae1.png
---

마이그레이션 에러가 발생했습니다.

무작정 migrations 파일을 지우지 말고 해결해 보아요!
<!--more-->

### Conflicting migrations detected; multiple leaf nodes in the migration graph

```
CommandError: Conflicting migrations detected; multiple leaf nodes in the migration graph: 
(0002_faq_alter_applicant_certificate_and_more, 0009_alter_meeting_user in recruit; 
0002_remove_portfolio_problem_remove_portfolio_solution_and_more, 0015_alter_portfoliosubject_portfolio in portfolio; 
0002_gallery_setting_delete_gallary_and_more, 0012_alter_setting_last_recruit_end_day_and_more in main).
To fix them run 'python manage.py makemigrations --merge'
```

이런 오류가 발생했습니다.

보아하니, recruit app에서는 두 개의 파일이, portfolio 앱에서도 두 개의 파일이, main 앱에서도 두 개의 파일이 충돌하고 있네요.

leaf node가 여러 개라고 하는 것을 보니 migrations 파일들이 서로 연결이 되어 있나봐요.

migrations 파일을 자세히 보면 위에 `dependencies`가 존재합니다.

```python
class Migration(migrations.Migration):

    dependencies = [
        ('main', '0002_alter_curriculum_created_at_and_more'),
    ]
...
```

번호 순서대로 skewed tree처럼 일렬로, 순서대로 연결되어 있어요.

그런데 중간에 갈라지는 파일이 생겨 버리면 저런 오류가 발생합니다.

![디렉토리 구조](/images/2024/06/19/01migration.jpeg)

따라서 <mark>중간에 갈라지는 파일만 삭제하면 됩니다.</mark>

위 이미지에서는 `0002_remove_portfolio...` 파일이죠!

### __init__ 파일만 남기고 다 지우세요?

라는 글이 많이 보입니다.

<mark>이런 경우 데이터베이스 자체도 다 날려야 하는 상황이 발생할 수 있습니다.</mark>

**함부로 삭제하면 안 돼요!!**

저는 데이터베이스를 삭제할 수 없었으므로 migrations 파일을 최대한 보존해야 했어요.

### model 파일 내부 함수를 제거하게 되어도

migrate에서 오류가 발생할 수 있습니다.

models.py 파일 안에서만 사용하는 함수인 `upload_to_func`를 제거했는데, migrations 파일에서 해당 함수를 찾을 수 없어서 발생하는 문제인데요.

![오류](/images/2024/06/19/02modelfunction.png)

이런 경우 migartions 파일 전체를 삭제하지 않고, <mark>migrations 파일 안에서 해당 함수를 사용하는 부분을 모두 변경해 주어도 됩니다.</mark>

![Before](/images/2024/06/19/03editmigrations.png)
![After](/images/2024/06/19/04editmigrations.png)

### 참고

- [좋은 글](https://www.jgahn.com/post/7)