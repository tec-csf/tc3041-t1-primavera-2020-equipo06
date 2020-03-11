# Create database scripts

## 1. School
```
CREATE TABLE SCHOOL (
	id INTEGER NOT NULL,
	start_date DATE NOT NULL,
	end_date DATE NOT NULL,
	sys_start TIMESTAMP(12) NOT NULL GENERATED ALWAYS AS ROW
BEGIN
,
	SYS_END TIMESTAMP(
		12 )
	NOT NULL GENERATED ALWAYS AS ROW
END,
create_id timestamp(
	12 )
GENERATED always AS TRANSACTION START id,
PERIOD BUSINESS_TIME (
	start_date,
	end_date ),
PERIOD SYSTEM_TIME (
	SYS_START,
	SYS_END ),
PRIMARY KEY (
	id ) )

CREATE TABLE HIST_SCHOOL LIKE school ALTER TABLE school
	ADD versioning use history TABLE hist_school

CREATE UNIQUE INDEX index_SCHOOL ON School (id, BUSINESS_TIME WITHOUT OVERLAPS)
```

## 2. Board
```
CREATE TABLE BOARD (
	id INTEGER NOT NULL,
	school_id INTEGER NOT NULL,
	letter VARCHAR(1) NOT NULL,
	empty_votes INTEGER NOT NULL,
	null_votes INTEGER NOT NULL,
	sys_start TIMESTAMP(12) NOT NULL GENERATED ALWAYS AS ROW
BEGIN
,
	SYS_END TIMESTAMP(
		12 )
	NOT NULL GENERATED ALWAYS AS ROW
END,
create_id timestamp(
	12 )
GENERATED always AS TRANSACTION START id,
PERIOD SYSTEM_TIME (
	SYS_START,
	SYS_END ),
PRIMARY KEY (
	id ),
FOREIGN KEY (
	school_id )
REFERENCES SCHOOL (
	id ))

CREATE TABLE hist_board LIKE board ALTER TABLE board
	ADD versioning use history TABLE hist_board
```

## 3. Party
```
CREATE TABLE PARTY (
	acronym VARCHAR(10) NOT NULL,
	name VARCHAR(45) NOT NULL,
	president_name VARCHAR(45) NOT NULL,
	sys_start TIMESTAMP(12) NOT NULL GENERATED ALWAYS AS ROW
BEGIN
,
	SYS_END TIMESTAMP(
		12 )
	NOT NULL GENERATED ALWAYS AS ROW
END,
create_id timestamp(
	12 )
GENERATED always AS TRANSACTION START id,
PERIOD SYSTEM_TIME (
	SYS_START,
	SYS_END ),
PRIMARY KEY (
	acronym ))
CREATE TABLE hist_party LIKE Party ALTER TABLE party
	ADD versioning use history TABLE hist_party
```

## 4. Voter
```
CREATE TABLE VOTER (
	ine_pasaport VARCHAR(45) NOT NULL,
	name VARCHAR(45) NOT NULL,
	dob DATE NOT NULL,
	address VARCHAR(100) NOT NULL,
	board_id INTEGER NOT NULL,
	party_acronym VARCHAR(10) NOT NULL,
	attorney BOOLEAN NOT NULL,
	is_mexican BOOLEAN NOT NULL,
	federal_municipal boolean NOT NULL,
	sys_start TIMESTAMP(12) NOT NULL GENERATED ALWAYS AS ROW
BEGIN
,
	SYS_END TIMESTAMP(
		12 )
	NOT NULL GENERATED ALWAYS AS ROW
END,
create_id timestamp(
	12 )
GENERATED always AS TRANSACTION START id,
PERIOD SYSTEM_TIME (
	SYS_START,
	SYS_END ),
PRIMARY KEY (
	ine_pasaport, board_id, party_acronym ),
FOREIGN KEY (
	party_acronym )
REFERENCES PARTY (
	acronym ),
FOREIGN KEY (
	board_id )
REFERENCES BOARD (
	id ))
CREATE TABLE hist_voter LIKE voter ALTER TABLE voter
	ADD versioning use history TABLE hist_voter
```

## 5. Roster
```
CREATE TABLE ROSTER (
	ine VARCHAR(45) NOT NULL,
	name VARCHAR(45) NOT NULL,
	party_acronym VARCHAR(10) NOT NULL,
	order INTEGER NOT NULL,
	START_DATE TIMESTAMP(6) NOT NULL,
	END_DATE TIMESTAMP(6) NOT NULL,
	sys_start TIMESTAMP(12) NOT NULL GENERATED ALWAYS AS ROW
BEGIN
,
	SYS_END TIMESTAMP(
		12 )
	NOT NULL GENERATED ALWAYS AS ROW
END,
create_id timestamp(
	12 )
GENERATED always AS TRANSACTION START id,
PERIOD BUSINESS_TIME (
	START_DATE,
	END_DATE ),
PERIOD SYSTEM_TIME (
	SYS_START,
	SYS_END ),
PRIMARY KEY (
	ine ),
FOREIGN KEY (
	party_acronym )
REFERENCES PARTY (
	acronym ))
CREATE TABLE hist_roster LIKE roster ALTER TABLE roster
	ADD versioning use history TABLE hist_roster CREATE UNIQUE INDEX index_roster ON roster (
		ine, BUSINESS_TIME WITHOUT OVERLAPS )
```

## 6. Substitute
```
CREATE TABLE SUBSTITUTE (
	substitute_ine VARCHAR(45) NOT NULL,
	voter_board_id INTEGER NOT NULL,
	voter_party_acronym VARCHAR(10) NOT NULL,
	start_date DATE NOT NULL,
	end_date DATE NOT NULL,
	sys_start TIMESTAMP(12) NOT NULL GENERATED ALWAYS AS ROW
BEGIN
,
	SYS_END TIMESTAMP(
		12 )
	NOT NULL GENERATED ALWAYS AS ROW
END,
create_id timestamp(
	12 )
GENERATED always AS TRANSACTION START id,
PERIOD BUSINESS_TIME (
	start_date,
	end_date ),
period SYSTEM_TIME (
	SYS_START,
	SYS_END ),
PRIMARY KEY (
	substitute_ine ),
FOREIGN KEY (
	substitute_ine,
	voter_board_id,
	voter_party_acronym )
REFERENCES VOTER (
	ine_pasaport,
	board_id,
	party_acronym ))
CREATE TABLE hist_substitute LIKE substitute ALTER TABLE substitute
	ADD versioning use history TABLE hist_substitute CREATE UNIQUE INDEX index_period_SUBSTITUTE ON SUBSTITUTE (
		substitute_ine, BUSINESS_TIME WITHOUT OVERLAPS )
```

## 7. Member
```
CREATE TABLE MEMBER (
	voter_ine VARCHAR(45) NOT NULL,
	voter_board_id INTEGER NOT NULL,
	voter_party_acronym VARCHAR(10) NOT NULL,
	vocal_president BOOLEAN NOT NULL,
	substitute_ine VARCHAR(45) NOT NULL,
	start_date DATE NOT NULL,
	end_date DATE NOT NULL,
	sys_start TIMESTAMP(12) NOT NULL GENERATED ALWAYS AS ROW
BEGIN
,
	SYS_END TIMESTAMP(
		12 )
	NOT NULL GENERATED ALWAYS AS ROW
END,
create_id timestamp(
	12 )
GENERATED always AS TRANSACTION START id,
PERIOD BUSINESS_TIME (
	start_date,
	end_date ),
period SYSTEM_TIME (
	SYS_START,
	SYS_END ),
FOREIGN KEY (
	voter_ine,
	voter_board_id,
	voter_party_acronym )
REFERENCES VOTER (
	ine_pasaport,
	board_id,
	party_acronym ),
FOREIGN KEY (
	substitute_ine )
REFERENCES SUBSTITUTE (
	substitute_ine ))
CREATE TABLE hist_member LIKE member ALTER TABLE member
	ADD versioning use history TABLE hist_member CREATE UNIQUE INDEX index_period_MEMBER ON MEMBER (
		voter_ine, BUSINESS_TIME WITHOUT OVERLAPS )
```