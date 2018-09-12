# 지옥에서 온 Git
- [https://opentutorials.org/course/2708](https://opentutorials.org/course/2708)

## VCS (Version Control System)
- CVS
- SVN
- GIT

## 버전관리의 본질

### Git 설치
- [https://git-scm.com/](https://git-scm.com/)

### 로컬 저장소 만들기
- git으로 버전관리 할 폴더(프로젝트) 초기화
~~~
$ git init
~~~

### git이 관리할 대상으로 파일 등록
- 파일 추적
~~~
$ git add [파일명]
~~~

- 프로젝트 폴더 상태 확인
~~~
$ git status
~~~

### 버전 만들기
- 버전 != 변화
- 버전 : 어떠한 작업이 완료된 상태

- git client 닉네임/이메일 설정
~~~
$ git config --global user.name [닉네임]
$ git config --global user.email [이메일]
~~~
> ~/.gitconfig 파일 내 저장됨

- 커밋 명령어
~~~
$ git commit
~~~
> 커밋 명령어 입력 후 vim 에디터가 출력되며 commit message를 입력 후 저장해야 커밋된다.

- 커밋 로그 확인
~~~
$ git log
~~~

### Stage area
- git add 하지 않다면 stage 영역에 올라오지 않았고, 커밋되지 않는다.
> Stage area : commit 대기상태
> Repository : commit 된 결과가 저장되는 곳

### 변경사항 확인하기
- 커밋과 커밋 상 소스간 차이점
~~~
$ git log -p
~~~

- 특정 커밋ID 부터 이전 변경사항만 확인
~~~
$ git log [커밋ID]
~~~

- 버전 간 차이점 비교
~~~
$ git diff [커밋ID]..[커밋ID]
~~~

- add하기 전과 add한 후의 파일 내용을 비교현재 파일과 마지막 버전 간 차이점 비교
~~~
$ git diff
~~~

### 과거 버전으로 돌아가기 (!!!)
- 특정 커밋ID로 reset하기
~~~
$ git reset [커밋ID] --hard
~~~
> 공유한 이후 (remote repository에 push???)에는 reset하면 안돼...

- revert는 특정 커밋ID를 취소하고 새로운 버전으로 만든다
~~~
$ git revert [커밋ID]
~~~
> push 했을 경우에는 쓸만하다... 그러나 conflict 날 수 있다...

- [reset / revert 차이점](http://www.popit.kr/)

- 현재 체크아웃된 브랜치의 최근 마지막 버전으로 돌리기
~~~
$ git reset --hard HEAD
~~~

### 명령의 빈도와 메뉴얼 보는 방법
- 명령어 도움말
~~~
git commit --help
~~~


## git의 원리
### gistory 설치
~~~
$ sudo pip install gistory
~~~


### git add의 원리
- git에서 파일 add되면 objects 내 파일이 추가된다.
- index에는 실제 파일명이 어떤 object에 매핑되는가에 대한 정보가 저장된다.
- git은 어떠한 파일을 저장할 때, 파일의 이름이 달라도, 파일의 내용이 같으면 같은 Object 파일을 가리킨다.

### objects 파일명의 원리
- 내용이 같으면 objects 파일도 같다
- sha1과 같은 특정값에 대한 해쉬값을 생성
- 해쉬값 앞 2자리 : objects 밑의 디렉토리명
- 해시값 나머지값 : objects 파일명
> aaf4c61ddaaef : objects/aa/f4c61ddaaef

### commit의 원리
- 커밋의 주요한 정보 2개 (이전커밋정보 parent, 커밋이 일어난 시점의 작업폴더 내 파일의 이름 + 파일이 담고 있는 내용에 대한 정보(blob))
- 각각의 버전마다 서로다른 tree가 있다
- 버전이 만들어진 시점에 프로젝트폴더에 대한 상태를 얻어낼 수 있다 -> Snapshot

### Object는 총 3가지 형태가 존재
- blob : 파일의 내용
- tree : 작업폴더 내 다른 작업폴더(tree), 파일의 이름 + 파일이 담고 있는 내용에 대한 정보(blob)
- commit : tree + parent commit + 작성자 정보 및 commit 메시지

### status의 원리
- stage 전 : index의 값과 실제 파일 차이
- stage 후 : index가 새로 변경되었으나, 가장 최신 커밋의 tree에서 가리키는 object와 다르기 때문에 commit 대기상태임을 알 수 있다.
- 변경사항 없을 경우 : 실제 파일 + index + commit tree가 일치한다.

### working directory VS index(staging area, cache) VS repository
![working directory vs index vs repository](https://i.stack.imgur.com/caci5.png)


## git의 혁신 - branch
- svn에 비해 branch가 쓸만한 수준이 되었다

### branch 만들기
- branch 목록출력
~~~
$ git branch
~~~

- branch 생성
~~~
$ git branch [새로운 브랜치 이름]
~~~

- branch를 전환(체크아웃) 할 때
~~~
$ git checkout [브랜치 명]
~~~

- branch 생성 및 전환(체크아웃)
~~~
$ git checkout -b [새로운 브랜치 명]
~~~

- branch 삭제
~~~
$ git branch -d
~~~

- 병합하지 않은 branch 강제 삭제
~~~
$ git branch -D
~~~

### branch 정보 확인
- 로그에 모든 브랜치를 표시하고, 그래프를 표시하고, 브랜치명을 표시하고, 한줄로 표시할 때
~~~
$ git log --branches --graph --decorate --oneline
~~~

- branch간 커밋 비교 (앞 브랜치명에는 없고 뒤 브랜치명에는 있는 것)
~~~
$ git log [앞 브랜치명]..[뒤 브랜치명]
~~~

- branch간 커밋 비교 (앞 브랜치명에는 없고 뒤 브랜치명에는 있는 것) 소스코드까지 비교
~~~
$ git log -p [앞 브랜치명]..[뒤 브랜치명]
~~~

- 각 branch 간 현재 상태 비교
~~~
$ git diff [비교할 브랜치명 1]..[비교할 브랜치명 2]
~~~

### branch 병합 (merge)
- A 브랜치로 B 브랜치를 병합할 때 (A <- B)
~~~
$ git checkout A
$ git merge B
~~~

### branch 병합 심화 (fast forward vs merge commit)
- fast-forward : 빨리감기 -> 별도의 커밋을 생성하지 않는다.
- Merge made by the 'recursive' strategy : merge commit을 생성한다는 내용.

### branch 병합 시 충돌 해결
- 서로다른 branch에서 서로 다른 파일에 대한 작업일 경우, 병합 시 자동으로 병합처리해준다.
- 파일이 같을 경우 문제 발생할 수 있다.
- 충돌났을 때 파일 내용
~~~
<<<<<<< HEAD(현재브랜치)
충돌내용1-현재브랜치
=======
충돌내용1-병합하려는 브랜치
>>>>>>> [병합하려는 브랜치]
~~~

- 충돌난 구간 내 <<<<<<<, =======, >>>>>>> 기호를 제거한 후 git add 명령어로 충돌작업을 끝냈음을 git에 알려준다.
~~~
$ git add [충돌 난 파일 명]
~~~


### stash
- 숨겨주다, 감추다
- stack 구조이다.
- tracked file만 stash 된다.
- 작업중인 내용 stash에 저장(push)하기
~~~
$ git stash [push]
~~~

- stash에서 가장 최근에 숨겼던 작업내용 가져오기
~~~
$ git stash apply
~~~

- stash 리스트 출력
~~~
$ git stash list
~~~

- stash 중 가장 최신 내용 삭제
~~~
$ git stash drop
~~~

- stash apply + drop
~~~
$ git stash pop
~~~

## git의 원리
### branch의 원리
- head 파일 : ref: refs/heads/master
- head는 단순히 파일이다.
- branch를 새로 따면 refs/heads/[새로운 브랜치명] 파일이 생성되며, 해당 브랜치파일에는 특정 커밋 오브젝트 정보를 가리킨다.

### reset checkout의 원리
- 과거로 돌아갈 **수** 있다.
~~~
$ git reset --hard [커밋ID]
~~~
- HEAD -> refs/heads/master -> 커밋ID 오브젝트
- Git은 웬만해서는 파일을 삭제하지 않는다.
- reset을 취소하고 싶다면??? -> ORIG_HEAD 또는 logs/refs/heads/master를 확인한다.
- logs/..는 해당 브랜치에서 발생한 모든 이력을 확인할 수 있다.
- ORIG_HEAD는 최근에 실행된 어떤 위험한 명령 (정보를 잃어버릴 수 있는...)에 대해 저장한다.
- ORIG_HEAD를 통해 복원할 수 있다.
~~~
$ git reset --hard ORIG_HEAD
~~~
- 이전에 했던 명령의 모든 것을 확인할 수 있다.
~~~
$ git reflog
~~~

- checkout 뒤에 커밋 아이디를 직접 적을 수 있다.
~~~
$ git checkout [커밋ID]
~~~
- HEAD 파일은 브랜치가 아닌 특정 커밋ID를 바라볼 수 있다. -> Deteched 상태

### reset으로 알아보는 working copy, index, repository
- reset의 옵션 : --soft, --mixed, --hard
- 아래 옵션에 따라 해당 영역을 되돌린다.
- --hard (가장 심플하다) : working directory, index, repository
- --mixed (옵션 없을 경우 default 옵션): index, repository
- --soft : repository

- working directory == working tree == working copy
- index == staging area == cache
- repository == history == tree (커밋된 버전을 의미)

- working directory와 index 비교명령어
~~~
$ git diff
~~~

- reset 쓰는 케이스 : 커밋하기 전 작업이 마음에 안들어서 과거로 돌리고 싶을 때...


### merge & conflict 원리
- index파일 내 충돌 일어났을 때, 해당 파일 및 숫자 1/2/3이 붙음 -> 3 way merge
- 3 way merge : 공통이 되는 파일 + 각자의 파일 2개 -> git에서 자동 병합 시도
- merge도 위험한 작업이므로, 이전으로 돌아갈 수 있도록 ORIG_HEAD에 내용이 들어간다.
- 병합을 전문적으로 해주는 프로그램 사용하면 좋다. (ex. kdiff3, bc(beyond compare))
- kdiff3 설정 명령어
~~~
$ git config --global merge.tool kdiff3
$ git config --global mergetool.kdiff3.path /Applications/kdiff3.app/Contents/MacOS/kdiff3
~~~
- merge툴을 사용한 merge 실행
~~~
$ git mergetool
~~~
- base - 공통소스 / local - checkout되어있는 소스 / remote - merging을 당하는 소스

### 3 Way Merge
- 2 way merge는 현재 브랜치와 머지할 브랜치만 고려한다.
- 3 way merge는 현재 브랜치, 머지할 브랜치 및 공통 소스를 고려한다.
|me  |base |other |2 way merge |3 way merge |
|----|-----|------|------------|------------|
|A   |A    |      |?           |            |
|B   |B    |B     |B           |B           |
|1   |C    |2     |?           |?           |
|    |D    |D     |?           |            |


## 원격저장소
- 저장소로써의 역할만 하는 bare
~~~
$ git init --bare remote-repo
~~~
> working directory가 없는 .git내에 있는 내용물만 있다.

- remote 추가
~~~
$ git remote add [원격저장소 이름(보통 origin)] [원격저장소 경로]
~~~

- remote 삭제
~~~
$ git remote remove [원격저장소 이름]
~~~

- remote에 올리기
~~~
$ git push
~~~

- push옵션 설정
- simple : 현재 작업중인 브랜치만 push
- matching : local과 remote 브랜치명이 동일한 모든 브랜치를 push
~~~
$ git config --global push.default [simple/matching]
~~~

- 현재 브랜치(master)에서 git push하면 origin 원격저장소의 master에 up-stream(업로드) 한다.
~~~
$ git push
$ git push --set-upstream origin master
~~~

## github
### 원격 저장소를 지역 저장소로 복제
- 원격저장소로부터 저장소 clone
~~~
$ git clone https://github.com/git/git.git [저장소폴더명(option)]
~~~

- git log를 거꾸로 출력
~~~
$ git log --reverse
~~~

- git의 첫번째 커밋으로 체크아웃하기
~~~
$ git checkout [첫번째 커밋 ID]
~~~

### 원격저장소 만들기
- 로컬저장소에서 원격저장소를 추가하기
~~~
$ git remote add origin https://github.com/hojinjosephchoi/gitfth.git
~~~

- git remote 확인 (-v 상세보기 옵션)
~~~
$ git remote -v
~~~

- git 로컬저장소의 현재 브랜치를 remote의 master 브랜치에 동기화
~~~
$ git push -u origin master
~~~

### 원격 저장소와 지역 저장소의 동기화 방법
- 마지막 commit된 메시지 수정 (개정하다) -> 원격저장소 올리기 전에만 가능
~~~
$ git commit --amend
~~~
- 원격 저장소로부터 당겨오는 작업
~~~
$ git pull
~~~

### 로그인 없이 원격 저장소 이용하기 (github ssh)
- ssh 비밀키/공개키 생성
~~~
$ ssh-keygen
~~~
> ~/.ssh 내 id_rsa / id_rsa.pub 생성

## 원격 저장소의 원리
- ./.git/config 내 remote 정보 수정
- refs/remotes/origin/[브랜치명] 오브젝트 파일 생성됨

### pull VS. fetch
- pull : 필요한 파일 다운받고 병합도 같이 한다.
- fetch : 필요한 파일만 다운로드 받는다. (병합은 따로 해야 한다. -> 신중한 작업 시 유용)

- master, origin/master가 같은 커밋을 가리키고 있다.
- refs/remotes/origin/master 및 refs/heads/master가 모두 변경된다.
~~~
$ git pull
~~~

- origin/master가 local master를 앞지를 수 있다.
- refs/remotes/origin/master만 변경하며, refs/heads/master는 변경하지 않는다.
~~~
$ git fetch
~~~
- fetch장점 : 원격 저장소의 내용과 로컬 저장소 내용의 차이점을 확인할 수 있는 장점이 있다.
~~~
$ git diff HEAD origin/master
~~~
- 이후 원격 저장소 내용을 현재 브랜치(로컬 저장소)에 merge 할 수 있다.
~~~
$ git merge origin/master
~~~

## TAG
- branch는 항상 바뀐다
- tag는 특정 커밋ID만을 가리킨다. 언제나 똑같은 것을 지정한다.

- 태그 추가 (light weight tag)
~~~
$ git tag [태그명]
$ git tag [태그명] [커밋ID]
$ git tag [태그명] [브랜치명]
~~~

- 특정 태그 시점으로 돌아갈 수 있다.
~~~
$ git checkout [태그명]
~~~

- 태그에 대한 자세한 설명을 붙이고 싶을 때 (주석을 추가하고 싶을 때) annotated tag를 추가한다.
~~~
$ git tag -a [태그명] -m [주석내용]
$ git tag -a [태그명] -m [주석내용] [커밋ID]
$ git tag -a [태그명] -m [주석내용] [브랜치명]
~~~

- 특정 태그에 대한 상세내용 확인
~~~
$ git tag -v [태그명]
~~~

- 태그를 원격저장소에 보내기
~~~
$ git push --tags
~~~

- 태그 삭제하기
~~~
$ git tag -d [태그명]
~~~

- light weight tag 생성 시 .git내 refs/tags/[태그명] 내 특정 커밋ID가 포함된 오브젝트가 생성된다.

- annotated tag 생성 시 .git내 refs/tags/[태그명] 내 특정 오브젝트ID가 포함되고, 특정 오브젝트 내에는 주석 + 특정 커밋ID가 있다.

- [버저닝 관련 문서 - semantic versioning](https://semver.org/lang/ko/)


## rebase
- rebase VS. merge
- 똑같이 병합하는 것이지만, 결과가 조금 다르다
~~~
m -> f merge 시 : git checkout f, git merge m
m -> f rebase 시 : git checkout f, git rebase m
~~~
- rebase는 임시저장소에 feature가 만들어 놓은 커밋을 저장한다.
- 공통점 : 최종 합쳐진것은 동일
- 차이점 : merge 는 히스토리가 병렬, rebase는 히스토리가 한 줄로 보기 쉽다. (fast-forwarding 할 수 있다.)
- rebase는 어렵고 위험
- merge는 쉽고 상대적으로 안전

- rebase는 다른사람과 공유하지 않은 커밋에 대해서만 작업해야 한다. (pull -> rebase -> push)

### rebase 시 충돌
- 충돌 해결 -> git add -> git rebase --continue

## Gitflow
- git을 잘 사용하기 위한 방법론
- [A successful Git branching model](https://nvie.com/posts/a-successful-git-branching-model/)
- master / develop 브랜치
- feature : 특정한 기능 개발에 대한 브랜치
- release 브랜치 : 배포용 브랜치 -> 버그 픽스하고, master / develop 브랜치에 머지시킨다.
- hotfix : 긴급 반영 건
- pull / push는 자주해준다.

- feature 브랜치는 develop 브랜치 내용을 수시로 받는다 (merge)
- master 브랜치는 권한을 갖고 있는 누군가가 엄격하게 관리하는 것이 좋다.
