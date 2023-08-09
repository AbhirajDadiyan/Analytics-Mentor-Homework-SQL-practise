# --These are my solutions to practise questions from Analytics Mentor SQL for Beginners Course

/* date created: august 9th 2023
created by: Abhiraj Singh Dadiyan
description: homework/practice for analytics mentor course*/

--Need to better understand the food and inventory that we have in our kitchen

/* count the number of food records*/
--count the number of food items

select
	 count(*)
from
	foods;
	

--selecting all the columns in the table without using *	
select
	 f.food_id
	,f.item_name
	,f.storage_type
	,f.package_size
	,f.package_size_uom
	,f.brand_name
	,f.package_price
	,f.price_last_updated_ts
from
	 foods f;
	 
--selecting all the columns in the table but having 
--price_last_updated_ts as first column

select
	 f.price_last_updated_ts
	,f.food_id
	,f.item_name
	,f.storage_type
	,f.package_size
	,f.package_size_uom
	,f.brand_name
	,f.package_price
from
	 foods f;
	 
--changinge package_size_uom to something that is not cryptic to viewer

select
	 f.food_id
	,f.item_name
	,f.storage_type
	,f.package_size
	,f.package_size_uom as "package_size_unit_of_measurement"
	,f.brand_name
	,f.package_price
	,f.price_last_updated_ts
from
	 foods f;
	 
--what brands are private label?

select
	 f.food_id
	,f.item_name
	,f.storage_type
	,f.package_size
	,f.package_size_uom as "package_size_unit_of_measurement"
	,f.brand_name
	,f.package_price
	,f.price_last_updated_ts
from
	foods f
where
	brand_name = 'H-E-B (private label)';
--using lower to match case sensitivity	
select
	 f.food_id
	,f.item_name
	,f.storage_type
	,f.package_size
	,f.package_size_uom as "package_size_unit_of_measurement"
	,f.brand_name
	,f.package_price
	,f.price_last_updated_ts
from
	foods f
where
	lower(brand_name) = 'h-e-b (private label)'
	
--using ilike to match case sensitivity

select
	 f.food_id
	,f.item_name
	,f.storage_type
	,f.package_size
	,f.package_size_uom as "package_size_unit_of_measurement"
	,f.brand_name
	,f.package_price
	,f.price_last_updated_ts
from
	foods f
where
	brand_name ilike '%private label%';

select
	 f.food_id
	,f.item_name
	,f.storage_type
	,f.package_size
	,f.package_size_uom as "package_size_unit_of_measurement"
	,f.brand_name
	,f.package_price
	,f.price_last_updated_ts
from
	foods f
where
	upper(brand_name) = 'H-E-B (PRIVATE LABEL)';
	
--adding a column for is_canned
select
	 f.food_id
	,f.item_name
	,f.storage_type
	,f.package_size
	,f.package_size_uom as "package_size_unit_of_measurement"
	,f.brand_name
	,f.package_price
	,f.price_last_updated_ts
	,case when item_name ilike '%canned%'then 'Y' else 'N' end as is_canned_yn
from
	foods f;

--locate all the records where there is null values for brand name

select
	 f.food_id
	,f.item_name
	,f.storage_type
	,f.package_size
	,f.package_size_uom as "package_size_unit_of_measurement"
	,f.brand_name
	,f.package_price
	,f.price_last_updated_ts
from
	foods f
where
	brand_name is null; --no null value found in brand name
	

--count each of brand_name

select
     f.brand_name
	,count(*) as record_count
from
	foods f
group by
	f.brand_name
order by
	brand_name desc;


--selecting unique brand names
select
	 distinct(f.brand_name)
from
	foods f;
	
select
	distinct(f.storage_type)
from
	foods f;
	
update foods
	set storage_type = 'unknown'
	where storage_type is null;
--selecting items with food_id 13,15 and 17	
select
	*
from
	foods f
where
	food_id in ('13','15','17');
	
--selecting items with food_id 13,15 and 17	as well as brand_name having a private label

select
	*
from
	foods f
where
	food_id in ('13','15','17')
and brand_name ilike '%private label%';

--finding out the percentage of private label foods in the foods table
select
	count(*) 
from
	foods;
	
select
	count(*)
from
	foods
where
	brand_name ilike '%private label%';


select 
	cast(n.heb_records as decimal (10,2))/
	cast(d.total_records as decimal (10,2)) 
from
(
	select
	count(*) as heb_records
from
	foods f
where
	f.brand_name ilike '%private label%'
	)n --numerator
cross join
(
select
	count(*) as total_records
from
	foods f
)d--denominator
;

--selecting all values for the price_last_updated_ts date
select
	distinct(f.price_last_updated_ts)
from
	foods f;
--Finding difference between current date and price_last_updated in days
--to figure out which product's prices need to be updated
select
	 f.food_id
	,f.item_name
	,f.storage_type
	,f.package_size
	,f.package_size_uom as "package_size_unit_of_measurement"
	,f.brand_name
	,f.package_price
	,f.price_last_updated_ts at time zone 'EST' as price_last_updated_est_ts
	,current_timestamp
	,(
			current_timestamp 
		-	(f.price_last_updated_ts at time zone 'EST')
	 ) as days_since_price_last_updated
	,current_Date
from
	foods f;
--finding difference between price_last_updated and current date in terms of date
--with and without hours minutes and seconds
select
	 f.food_id
	,f.item_name
	,f.storage_type
	,f.package_size
	,f.package_size_uom as "package_size_unit_of_measurement"
	,f.brand_name
	,f.package_price
	,f.price_last_updated_ts at time zone 'EST' as price_last_updated_est_ts
	,current_timestamp
	,(
			current_timestamp 
		-	(f.price_last_updated_ts at time zone 'EST')
	 ) as days_since_price_last_updated_w_ts
	,current_Date
	,current_Date - cast(
						 (f.price_last_updated_ts at time zone 'EST') as date
	 ) as days_since_last_update_wo_ts
from
	foods f;
	
--finding produts that have not been updated in more than 190 days at the time
--of this query
select
	*
from
		(select
			 f.food_id
			,f.item_name
			,f.storage_type
			,f.package_size
			,f.package_size_uom as "package_size_unit_of_measurement"
			,f.brand_name
			,f.package_price
			,f.price_last_updated_ts at time zone 'EST' as price_last_updated_est_ts
			,current_timestamp
			/*,(
					current_timestamp 
				-	(f.price_last_updated_ts at time zone 'EST')
			 ) as days_since_price_last_updated_w_ts 
			 */
			,current_Date
			,current_Date - cast(
								 (f.price_last_updated_ts at time zone 'EST') as date
			 ) as days_since_last_update_wo_ts
		from
			foods f) f--subquery
where
	f.days_since_last_update_wo_ts>188
	
--listing all the columns of both the food table and drinks table

select
	 f.food_id
	,f.item_name
	,f.storage_type
	,f.package_size
	,f.package_size_uom as package_Size_unit_of_measurement
	,f.brand_name
	,f.package_price
	,f.price_last_updated_ts
from
	foods f
	
union all

select
	 d.drink_id
	,d.item_name
	,d.storage_type
	,d.package_size
	,d.package_size_uom as package_Size_unit_of_measurement
	,d.brand_name
	,d.package_price
	,d.price_last_updated_ts
from
	drinks d;

----listing all the columns of both the food table and drinks table with source tables

select
	 f.food_id
	,f.item_name
	,f.storage_type
	,f.package_size
	,f.package_size_uom as package_Size_unit_of_measurement
	,f.brand_name
	,f.package_price
	,f.price_last_updated_ts
	,'foods_data' as source_table
from
	foods f
	
union all

select
	 d.drink_id
	,d.item_name
	,d.storage_type
	,d.package_size
	,d.package_size_uom as package_Size_unit_of_measurement
	,d.brand_name
	,d.package_price
	,d.price_last_updated_ts
	,'drinks_data' as source_table
from
	drinks d;
	
--listing all the columns of both the food table and drinks table with source tables
--and creating alias columns to differentiate between foodid and drinkid to cleanup
select
	 f.food_id--if this record is from food table, filling the column.if not, leave blank
	,null as drink_id--if this record is from the drink table,filling the column.if not, leave blank
	,f.item_name
	,f.storage_type
	,f.package_size
	,f.package_size_uom as package_Size_unit_of_measurement
	,f.brand_name
	,f.package_price
	,f.price_last_updated_ts
	,'foods_data' as source_table
from
	foods f
	
union all

select
	 d.drink_id --if this record is from drink table, filling the column.if not, leave blank
	,null as food_id--if this record is from the food table,filling the column.if not, leave blank
	,d.item_name
	,d.storage_type
	,d.package_size
	,d.package_size_uom as package_Size_unit_of_measurement
	,d.brand_name
	,d.package_price
	,d.price_last_updated_ts
	,'drinks_data' as source_table
from
	drinks d;
	
select
	 f.food_id
	,null as drink_id
	,f.item_name
	,f.storage_type
	,f.package_size
	,f.package_size_uom
	,f.brand_name
	,f.package_price
	,f.price_last_updated_ts
from
	foods f
union all
select
	 d.drink_id
	,null as food_id
	,d.item_name
	,d.storage_type
	,d.package_size
	,d.package_size_uom
	,d.brand_name
	,d.package_price
	,d.price_last_updated_ts
from
	drinks d;
	
/* date created: august 9th 2023
created by: Abhiraj Singh Dadiyan
description: homework/practise for analytics mentor course*/

--combining foods and food inventory table
select
	 f.food_id
	,f.item_name
	,f.storage_type
	,f.package_size
	,f.package_size_uom
	,f.brand_name
	,f.package_price
	,f.price_last_updated_ts
	,i.quantity as inventory_quantity
from
	foods f
		left join (
					select
						 i.food_inventory_id
						,i.food_item_id
						,i.quantity
						,i.inventory_dt
					from
						food_inventories i
					where
						 i.inventory_dt = (
											 select
												 max(i.inventory_dt) as max_inventory_Dt
												from
													food_inventories i)
		) i
	on
		f.food_id = i.food_item_id;

--outputting only the records where there is a match
select
	 f.food_id
	,f.item_name
	,f.storage_type
	,f.package_size
	,f.package_size_uom
	,f.brand_name
	,f.package_price
	,f.price_last_updated_ts
	,i.quantity as inventory_quantity
from
	foods f
		 join (
					select
						 i.food_inventory_id
						,i.food_item_id
						,i.quantity
						,i.inventory_dt
					from
						food_inventories i
					where
						 i.inventory_dt = (
											 select
												 max(i.inventory_dt) as max_inventory_Dt
												from
													food_inventories i)
		) i
	on
		f.food_id = i.food_item_id;
