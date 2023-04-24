1. (2 ქულა) დაუკავშირდით ორაკლ სერვერს როგორც პრივილეგირებული მომხმარებელი system და შექმენით როლი სკრიპტულად თქვენი გვარი_role და მიანიჭეთ შემდეგი სისტემური პრივილეგიები create session, create any table, create any view, create any index, create any sequence, create any synonym, alter any table, alter any index, alter any sequence, drop any table, drop any view, drop any index, drop any sequence, drop any synonym. მიანიჭეთ ობიექტ პრივილეგია მოსელექტება hr სქემის employees ცხრილზე. მიანიჭეთ როლი dba შექმნილ როლს.

სკრიპტი:


create role nozadze_role;


grant create session, create any table, create any view, create any index, create any sequence,

create any synonym, alter any table, alter any index, alter any sequence, drop any table,

drop any view, drop any index, drop any sequence, drop any synonym

to nozadze_role;

grant select on hr.employees to nozadze_role;

grant dba to nozadze_role;

2. (2 ქულა) სკრიპტულად შექმენი მომხმარებელი btu_გვარი პაროლი btu, დეფაულტ ცხრილსივრცე users ულიმიტოდ, დროებით ცხრილსივრცედ temp, პროფაილი default. გადაამოწმეთ იუზერის არსებობა. მიანიჭეთ შექმნილი როლი ამ მომხმარებელს.

სკრიპტი:
create user btu_nozadze identified by btu
default TABLESPACE users QUOTA UNLIMITED on users
TEMPORARY TABLESPACE temp
profile default;

select * from dba_users where username ='BTU_NOZADZE';

grant nozadze_role to btu_nozadze;

3. (1 ქულა) მიურთდით ბაზას როგორც მომხმარებელი btu_გვარი. გადაამოწმეთ ვინაობა. გადაამოწმეთ თუ ფლობს რაიმე ობიექტს.

სკრიპტი:
show user
select * from dba_objects where owner='BTU_NOZADZE';

4. (2 ქულა) შექმენი ცხრილი exam_გვარი რომელსაც ექნება 4 სვეტი: პირველი id რიცხვითი, members რიცხვითი (5) და მე-3 ვირტუალური სვეტი type,
რომელიც შეყვანილი სწევრების მიხედვით დააგენერირებს სვეტის მნიშვნელობას თუ შეტანილი იქნება members-ში 1000-დან 5000-ის ჩათვლით private,
თუ 5001-ზე მეტი share, მე-4 სვეტი appendix სიმბოლური მაქსიმუმ 50 სიმბოლო უჩინარი. შექმენი mem_idx ინდექსი members სვეტზე.

სკრიპტი:

create table exam_nozadze (id number, members number(5),
type generated always as (
case when members between 1000 and 5000 then 'private'
when members > 5001 then 'share' end),appendix varchar2(50) invisible);

create index mem_idx on exam_nozadze(members);


5. (1 ქულა) შექმენით მიმდევრობა seq_გვარი სახელით რომლის სტარტის წერტილია 12, ბიჯი 3, არ არის ციკლური,
არის მოწესრიგებული, არ იყოს ქეშირებული, მაქსიმალური მნიშვნელობა 2001 და მინიმალური მნიშვნელობა 12.

სკრიპტი:

create sequence seq_nozadze start with 12 INCREMENT BY 3 NOCACHE ORDER MAXVALUE 2001 MINVALUE 12;

6. (2 ქულა) შექმენი ტრიგერი exam_tr რომელიც ავტომატურ ზრდად id სვეტის მნიშვნელობებს დააგენერირებს ახალი ჩანაწერის შეტანისას exam_გვარი ცხრილში. 

სკრიპტი:

create or replace trigger exam_tr
before insert on exam_nozadze
for each row
begin
select seq_nozadze.nextval into :new.id from dual;
end;
/

7. (2 ქულა) შეიტანეთ exam_გვარი ცხრილში შემდეგი ჩანაწერები members და appendix სვეტებში

2450, 'Tbilisi, btu is the best'
11000, 'GTU cool exam'
3150, 'GAU this day'
5580, 'SEU perfect thing'
3890, 'ILIA UNI good idea'
7900, 'UG great option'

სკრიპტი:

insert into exam_nozadze(members, appendix) values (2450, 'Tbilisi, btu is the best');
insert into exam_nozadze(members, appendix) values (11000, 'GTU cool exam');
insert into exam_nozadze(members, appendix) values (3150, 'GAU this day');
insert into exam_nozadze(members, appendix) values (5580, 'SEU perfect thing');
insert into exam_nozadze(members, appendix) values (3890, 'ILIA UNI good idea');
insert into exam_nozadze(members, appendix) values (7900, 'UG great option');
commit;

8. (2 ქულა) შექმენით ქვემოთხოვნით ცხრილი emp სქემა hr-ის employees-ზე დაყრდნობით, საიდანაც აიღება მხოლოდ ის 
ჩანაწერები სადაც გვარის სიგრზე არის 7-სიმბოლოზე ნაკლები.
შექმენით ქვემოთხოვნით ცხრილი emp2 ცხრილ emp-ზე დაყრდნობით მხოლოდ კონსტრუქცია და არა ჩანაწერებიც.

სკრიპტი:

create table emp as
select * from hr.employees
where length(last_name)<7;

create table emp2 as
select * from emp
where 2=1;

select * from emp;
select * from emp2;

9. (2 ქულა) შექმენი my_exam_view ხედი emp ცხრილის პირველი 12 ჩანაწერით.

სკრიპტი:
create view my_exam_view as
select * from emp where rownum<=12;

10. (2 ქულა) შექმენი სინონიმები exam_გვარი ცხრილზე t_chakh (t_გვარის პირველი 3 ასო) სახელით და my_exam_view ხედზე vu სახელით.  გადაამაწმეთ თუ რა ობიექტებს ფლობს მომხმარებელი.

სკრიპტი:
create synonym test_noz for exam_nozadze;
create synonym vu for my_exam_view;

11. (2 ქულა)

a) შეიტანეთ ახალი ჩანაწერი ცხრილში test_გვარი 3000, 'my gtu forever';
b) დაამოდიფიცირეთ ჩანაწერი რომლის აიდია 21 appendix სვეტში არსებულს მიუკონკატენირეთ აიდი და members სვეტის მნიშვნელობა;
c) წაშალეთ ის სტრიქონები რომლებშიც appendix სვეტის სიგრძე არის 3-ის ჯერადი;  d) დაამატეთ სვეტი leader რიცხვითი;
e) სვეტი leader გახადეთ სიმბოლური მაქსიმალური 60 სიმბოლო;
f) გადაარქვით სახელი leader სვეტს boss სახელით;
g) ამოშალეთ სვეტი boss;
h) გახადეთ გამოუყენებელი appendix;
i) გადაარქვით ცხრილს  test_გვარი სახელი დაარქვით გვარის 5 ასო_tab;
j) გახადეთ ცხრილი მხოლოდ ამოკითხვადი.

-- a)

insert into exam_nozadze(members,appendix) values (3000,'my gtu forever');

-- b)

update exam_nozadze set appendix=appendix||id||members
where id = 21;

-- c)

delete from exam_nozadze where mod(length(appendix),3)=0;
commit;

-- d)

alter table exam_nozadze add leader number;

-- e)

alter table exam_nozadze modify leader varchar2(60);

-- f)

alter table exam_nozadze rename column leader to boss;

-- g)

alter table exam_nozadze drop column boss;

-- h)

alter table exam_nozadze set unused(appendix);

-- i)

alter table exam_nozadze rename to nozad_tab;

-- j)

alter table nozad_tab read only;


12. (2 ქულა) შექმენით ახალი პროფილი exam_prof_გვარი რომელსაც ექნება შემდეგი პარამეტრები

    COMPOSITE_LIMIT	default
    
    SESSIONS_PER_USER 	70
    
    CPU_PER_SESSION	unlimited
    
    CPU_PER_CALL 	unlimited
    
    LOGICAL_READS_PER_SESSION  700000
    
    LOGICAL_READS_PER_CALL	60000
    
    IDLE_TIME	12
    
    CONNECT_TIME 	900
    
    PRIVATE_SGA	default
    
    FAILED_LOGIN_ATTEMPTS	30
    
    PASSWORD_LIFE_TIME 	300
    
    PASSWORD_REUSE_TIME 	240
    
    PASSWORD_REUSE_MAX 	18
    
    PASSWORD_VERIFY_FUNCTION 	null
    
    PASSWORD_LOCK_TIME	10
    
    PASSWORD_GRACE_TIME	30
    

სკრიპტი:


CREATE PROFILE exam_prof_nozadze LIMIT

COMPOSITE_LIMIT default

SESSIONS_PER_USER 70

CPU_PER_SESSION unlimited

CPU_PER_CALL unlimited

LOGICAL_READS_PER_SESSION 700000

LOGICAL_READS_PER_CALL 60000

IDLE_TIME 12

CONNECT_TIME 900

PRIVATE_SGA default

FAILED_LOGIN_ATTEMPTS 30

PASSWORD_LIFE_TIME 300

PASSWORD_REUSE_TIME 240

PASSWORD_REUSE_MAX 18

PASSWORD_VERIFY_FUNCTION null

PASSWORD_LOCK_TIME 10

PASSWORD_GRACE_TIME 30;



13. (1 ქულა) დაამოდიფიცირეთ btu_გვარი იუზერის პარამეტრები პაროლი გახადეთ btu23  და პროფილი exam_prof_გვარი.

სკრიპტი:

alter user btu_nozadze identified by btu23 profile exam_prof_nozadze;

14. (1 ქულა) გააქტიურე აუდიტი btu_გვარი იუზერის განხორციელებულ ნებისმიერ ბრძანებაზე. ნახე აუდიტის ლოგი. აუდიტი გააუქმე.

სკრიპტი:


audit all statements by btu_nozadze;

select * from dba_audit_trail;

select * from DBA_STMT_AUDIT_OPTS;

select * from SYS.AUD$;

noaudit all statements by btu_nozadze;

15. (1 ქულა) წაშალეთ იუზერი btu_გვარი. წაშალეთ როლი გვარი_role. წაშალეთ პროფილი exam_prof_გვარი. გადაამოწმეთ არსებობს თუ არა ეს იუზერი.

სკრიპტი:


Drop user btu_nozadze cascade;

Drop role nozadze_role;

Drop profile exam_prof_nozadze;

select * from dba_users where username ='BTU_NOZADZE';
