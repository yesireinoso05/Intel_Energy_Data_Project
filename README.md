# Intel_Energy_Data_Project

#Intel,the semiconductor manufacturing powerhouse, is planning
on building a new data center. Energy availability and usage are some of the key
considerations in deciding on allocation of the data center. The task is to find which
regions produce a surplus of energy, and are therefore more likely to provide
energy at cheaper prices, as well as Which regions rely more on renewable energy sources.

# use intel.energy_data table to calculate the sum total of energy produced,grouped by each region and sorted by highest total energy. 
select
region,
sum(net_generation-demand) as total

From intel.energy_data
group by region
order by total desc

# lets find which region has the highest positive total energy
the mid-Atlantic region had the most total energy at 31. 7 million MW

select
region,
sum(hydropower_and_pumped_storage + wind + solar) as
new_column
from intel.energy_data
group by energy_data.region
order by new_column desc

# Calculate the sum total of renewable energy by region.
# Sort the output by the region with the highest renewable energy to find the top two regions.

select
region,
sum(hydropower_and_pumped_storage + wind + solar) as
new_column
from intel.energy_data
group by energy_data.region
order by new_column desc

The top two regions for total energy production is the Northwest
and Texas

# Now let's modify it so it calculates the percentage of renewable energy by region

select
region,
round(sum(hydropower_and_pumped_storage + wind + solar) /
sum(net_generation) * 100, 2)
as new_column
from intel.energy_data
group by energy_data.region
order by new_column desc

# regions that change from the top 3 when looking at total renewable energy vs percentage of renewable energy
Central and California are present as renewables.

# renewable energy generated for each row returning only the date, region, and energy_generated_mwcolumns.

select date
region,
hydropower_and_pumped_storage + wind + solar as
energy_gen_mw,
'renewable energy' as energy_type
from intel.energy_data
limit 20

# energy_generated_mw column is referring to renewable energy types. Now we create a new column called energy_type that has the value 
# ‘renewableenergy’for each row to be specific.

select date
region,
hydropower_and_pumped_storage + wind + solaras
energy_gen_mw,
'renewable energy' as energy_type
from intel.energy_data
limit 20

# calculate the fossil fuel energy generated for each row

select date
region,
all_petroleum_products + coal + natural_gas + nuclear +
other_fuel_sources,
'energy_generated_mw' as energy_type
from intel.energy_data
limit 20

# Modify to include the energy_type column

select date
region,
all_petroleum_products + coal + natural_gas + nuclear +
other_fuel_sources,
'fossil fuel' as energy_type
from intel.energy_data
limit 20

# Union them

select date
region,
hydropower_and_pumped_storage + wind + solaras
energy_gen_mw,
'renewable energy' as energy_type
from intel.energy_data
union all
select date
region,
all_petroleum_products + coal + natural_gas + nuclear +
other_fuel_sources,
'fossil fuel' as energy_type
from intel.energy_data
offset 1000000
limit 20

# Join the intel.power_plants and intel.energy_by_power_plant data on the plant_code.

select
*
from intel.power_plants as a
inner join intel.energy_by_plant as b
on a.plant_code = b.plant_code

2,504 rows

# Return the total number of renewable energies powerplants for each region.

WITH intel AS
(select
a.region,
sum(energy_generated_mw) as total_energy
from intel.power_plants as a
inner join intel.energy_by_plant as b
on a.plant_code = b.plant_code
group by a.region
order by total_energy desc)
select
*
From intel

The region with the most renewable powerplants is Midwest, with a total energy number of 834007002

# total number of powerplants and total energy generated from plants that use “Solar Photovoltaic” technology, grouped by each region

WITH intel AS
(select
primary_technology,
region,
sum(energy_generated_mw) as total_energy
from intel.power_plants as a
inner join intel.energy_by_plant as b
on a.plant_code = b.plant_code
where primary_technology = 'Solar Photovoltaic'
group by a.region, a.primary_technology
order by total_energy desc
)
select
*
From intel

# show regions having at least 50 power plants that use “Solar Photovoltaic” technology

(select
primary_technology,
region,
sum(energy_generated_mw) as total_energy
from intel.power_plants as a
inner join intel.energy_by_plant as b
on a.plant_code = b.plant_code
where primary_technology = 'Solar Photovoltaic'
group by a.region, a.primary_technology
HAVING SUM(energy_generated_mw) > 50
order by total_energy desc
)
select
*
From intel

#This tells me that the midwest ranks close to the middle in terms of how much energy is being produced in relation to other regions
