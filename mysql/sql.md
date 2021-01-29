## 集計関数
#### 基本（総数/合計/最大値/最小値/平均値）
```
--総数
select count(*) from mfs_3k.receivemails
where raijouflg = '0';
--合計
select sum(priority) from mfs_3k.portals;
--最大値
select max(priority) from mfs_3k.portals;
--最小値
select min(priority) from mfs_3k.portals;
--平均値
select avg(priority) from mfs_3k.portals;
```
#### グループ化(group by / having)
```
--グループ分け
select
	property_management_id as マネジメントID,
	sum(price) as 物件費用合計金額
from comforia.rooms
group by property_management_id
having 物件費用合計金額 >= 100000;


--パラメータの値を変更
select
	count(price) as 部屋の総数,
	max(price) as 部屋で一番高い金額,
	min(price) as 部屋で一番安い金額,
	avg(price) as 部屋の平均金額,
	sum(price) as 部屋の合計金額,
   (CASE
	    WHEN is_new_building = '1' THEN '新築マンション'
	    ELSE '旧マンション'
    END) AS マンションタイプ
from comforia.rooms
group by mansion_code
order by 部屋で一番高い金額 desc
```
#### 副問い合わせ（サブクエリ）
```
--副問い合わせ（サブクエリ）
select
	count(price) as 新築マンションの総数,
	max(price) as 新築マンションで一番高い金額,
	min(price) as 新築マンションで一番安い金額,
	avg(price) as 新築マンションの平均金額,
	sum(price) as 新築マンションの合計金額
from comforia.originals
where property_management_id in (
select property_management_id from comforia.rooms
where is_new_building = '1'
);
```
## 結合
#### 内部結合
```
--内部結合
select
	originals.mansion_name,
	rooms.room_number
from comforia.originals
inner join comforia.rooms on originals.mansion_code = rooms.mansion_code;
```
#### 左外部結合
```
--左外部結合
select
*
from comforia.originals
left join comforia.rooms on originals.mansion_code = rooms.mansion_code
limit 100;
```
#### 右外部結合
```
--右外部結合
select
*
from comforia.originals
right join comforia.rooms on originals.mansion_code = rooms.mansion_code;
```
#### 完全外部結合
```
--完全外部結合
select
	originals.mansion_name,
	rooms.room_number
from comforia.originals
left join comforia.rooms on originals.mansion_code = rooms.mansion_code
union
select
	originals.mansion_name,
	rooms.room_number
from comforia.originals
right join comforia.rooms on originals.mansion_code = rooms.mansion_code
limit 100;
```