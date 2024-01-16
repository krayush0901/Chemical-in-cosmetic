# Chemicals-in-cosmetics
--Questions Answered

--Find out which chemicals were used the most in cosmetics and personal care products
select chemicalname, count(chemicalname) as ChemicalsUsedTheMost
from ChemicalsIncosmetics
where ChemicalName is not null and PrimaryCategory = 'Personal Care Products'
group by ChemicalName
order  by count(chemicalname) desc

--Find out which companies used the most reported chemicals in their cosmetics and personal care products 
select companyname,count(Initialdatereportedconverted) as ChemicalsReported
from ChemicalsIncosmetics
where PrimaryCategory = 'Personal Care Products'
group by CompanyName
order by count(InitialdateReportedconverted) desc


--Which brands had chemicals that were removed and discontinued
select Brandname, Chemicalname,count(chemicaldateremoved) as DateRemoved,count(discontinueddate) as DateDiscontinued
from ChemicalsIncosmetics
where ChemicalDateRemoved is not null and discontinueddate is not null and chemicalname is not null 
group by ChemicalName
order by count(chemicaldateremoved) desc,count(discontinueddate) desc


--Identify the brands that had chemicals that were mostly reported in 2018
select brandname,count(InitialDateReportedconverted) as a
from ChemicalsIncosmetics
where InitialDateReportedconverted between '2018-01-01' and '2018-12-31'
group  by brandname
order by count(InitialDateReportedconverted) desc

--Which brands had chemicals discontinued and removed
select Brandname
from ChemicalsIncosmetics
where DiscontinuedDate is not null and ChemicalDateRemoved is not null
group by BrandName
order by count(DiscontinuedDate) desc, count(ChemicalDateRemoved) desc


--identify the period of creation of the removed chemicals and when they were actually removed
select Chemicalname,Chemicalcreatedconverted,ChemicalDateRemoved,
abs(datediff(month,chemicaldateremoved,chemicalcreatedconverted)) as removalperiod
from ChemicalsIncosmetics
where ChemicalDateRemoved is not null and ChemicalCreatedconverted is not null

--can you tell if discontinued chemicals in bath products were removed 
select Productname,Brandname,ChemicalName,
case 
    when discontinueddate is not null and chemicaldateremoved is not null 
	then 'yes'
	else 'no'
	end as DiscontinueStatus
	from ChemicalsIncosmetics
	where primarycategory='Bath Products'

--How long were the removed chemicals in baby products used ?
select Productname,Primarycategory,Chemicalname,ChemicalCreatedconverted,ChemicalDateRemoved,
abs(datediff(month,chemicaldateremoved,chemicalcreatedconverted)) as removalperiod
from ChemicalsIncosmetics
where PrimaryCategory = 'Baby Products'and
ChemicalDateRemoved is not null and
ChemicalCreatedconverted is not null

--identify the relationship between cemicals that were recently reported and discontinued (does most recently reported chemicals equal discontinuation of such chemicals 

select chemicalname,mostrecentdateconverted,discontinueddate,
case 
    when mostrecentdateconverted is not null and discontinueddate is not null
	then 'yes'
	else 'no'
	end as status
	from ChemicalsIncosmetics


--identify the relationship between the csf and chemicals used in the most manufactured subcategories
with CTE_sub_category
AS
 (select subcategory ,csf,count(subcategoryid) as sub_category_freq
from ChemicalsIncosmetics
group by SubCategory,CSF
)

select top (10) Subcategory,csf,sub_category_freq
from CTE_sub_category
where csf is not null
order by sub_category_freq desc
