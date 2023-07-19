# Vshaun-port-proj

![Dashboard 1](https://github.com/astromagenta/Vshaun-port-proj/assets/121637148/69b72ada-9865-4e7f-b351-83ad969f481d)
Tableu work book project https://public.tableau.com/views/Book1_16897871793710/Dashboard1?:language=en-US&:display_count=n&:origin=viz_share_link

/*

Queries used for Tableau Project

*/



-- 1. 

Select SUM(new_cases) as total_cases, SUM(cast(new_deaths as int)) as total_deaths, SUM(cast(new_deaths as int))/SUM(New_Cases)*100 as DeathPercentage
From PortfolioProject..CovidDeaths
--Where location like '%states%'
where continent is not null 
--Group By date
order by 1,2

-- Just a double check based off the data provided
-- numbers are extremely close so we will keep them - The Second includes "International"  Location


--Select SUM(new_cases) as total_cases, SUM(cast(new_deaths as int)) as total_deaths, SUM(cast(new_deaths as int))/SUM(New_Cases)*100 as DeathPercentage
--From PortfolioProject..CovidDeaths
----Where location like '%states%'
--where location = 'World'
----Group By date
--order by 1,2


-- 2. 

-- We take these out as they are not inluded in the above queries and want to stay consistent
-- European Union is part of Europe

Select location, SUM(cast(new_deaths as int)) as TotalDeathCount
From PortfolioProject..CovidDeaths
--Where location like '%states%'
Where continent is null 
and location not in ('World', 'European Union', 'International')
Group by location
order by TotalDeathCount desc


-- 3.

Select Location, Population, MAX(total_cases) as HighestInfectionCount,  Max((total_cases/population))*100 as PercentPopulationInfected
From PortfolioProject..CovidDeaths
--Where location like '%states%'
Group by Location, Population
order by PercentPopulationInfected desc


-- 4.


Select Location, Population,date, MAX(total_cases) as HighestInfectionCount,  Max((total_cases/population))*100 as PercentPopulationInfected
From PortfolioProject..CovidDeaths
--Where location like '%states%'
Group by Location, Population, date
order by PercentPopulationInfected desc












-- Queries I originally had, but excluded some because it created too long of video
-- Here only in case you want to check them out


-- 1.

Select dea.continent, dea.location, dea.date, dea.population
, MAX(vac.total_vaccinations) as RollingPeopleVaccinated
--, (RollingPeopleVaccinated/population)*100
From PortfolioProject..CovidDeaths dea
Join PortfolioProject..CovidVaccinations vac
	On dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null 
group by dea.continent, dea.location, dea.date, dea.population
order by 1,2,3




-- 2.
Select SUM(new_cases) as total_cases, SUM(cast(new_deaths as int)) as total_deaths, SUM(cast(new_deaths as int))/SUM(New_Cases)*100 as DeathPercentage
From PortfolioProject..CovidDeaths
--Where location like '%states%'
where continent is not null 
--Group By date
order by 1,2


-- Just a double check based off the data provided
-- numbers are extremely close so we will keep them - The Second includes "International"  Location


--Select SUM(new_cases) as total_cases, SUM(cast(new_deaths as int)) as total_deaths, SUM(cast(new_deaths as int))/SUM(New_Cases)*100 as DeathPercentage
--From PortfolioProject..CovidDeaths
----Where location like '%states%'
--where location = 'World'
----Group By date
--order by 1,2


-- 3.

-- We take these out as they are not inluded in the above queries and want to stay consistent
-- European Union is part of Europe

Select location, SUM(cast(new_deaths as int)) as TotalDeathCount
From PortfolioProject..CovidDeaths
--Where location like '%states%'
Where continent is null 
and location not in ('World', 'European Union', 'International')
Group by location
order by TotalDeathCount desc



-- 4.

Select Location, Population, MAX(total_cases) as HighestInfectionCount,  Max((total_cases/population))*100 as PercentPopulationInfected
From PortfolioProject..CovidDeaths
--Where location like '%states%'
Group by Location, Population
order by PercentPopulationInfected desc



-- 5.

--Select Location, date, total_cases,total_deaths, (total_deaths/total_cases)*100 as DeathPercentage
--From PortfolioProject..CovidDeaths
----Where location like '%states%'
--where continent is not null 
--order by 1,2

-- took the above query and added population
Select Location, date, population, total_cases, total_deaths
From PortfolioProject..CovidDeaths
--Where location like '%states%'
where continent is not null 
order by 1,2


-- 6. 


With PopvsVac (Continent, Location, Date, Population, New_Vaccinations, RollingPeopleVaccinated)
as
(
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as RollingPeopleVaccinated
--, (RollingPeopleVaccinated/population)*100
From PortfolioProject..CovidDeaths dea
Join PortfolioProject..CovidVaccinations vac
	On dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null 
--order by 2,3
)
Select *, (RollingPeopleVaccinated/Population)*100 as PercentPeopleVaccinated
From PopvsVac


-- 7. 

Select Location, Population,date, MAX(total_cases) as HighestInfectionCount,  Max((total_cases/population))*100 as PercentPopulationInfected
From PortfolioProject..CovidDeaths
--Where location like '%states%'
Group by Location, Population, date
order by PercentPopulationInfected desc



Python sql code

import pandas as pd
import numpy as np
df = pd.read_csv("CovidDeaths1.csv")
df = df.replace(np.nan, 0)
functions = dir(np)
print(functions)
continent_af = df[(df["continent"] == "Africa")]
continent_as = df[(df["continent"] == "Asia")]
continent_eu = df[(df["continent"] == "Europe")]
print(len(continent_af))
print(len(continent_as))
print(len(continent_eu))
print(continent_as.head())

continent_af.to_csv('continent_af.csv')
continent_as.to_csv('continent_as.csv')
continent_eu.to_csv('continent_eu.csv')


list(continent_af.columns[1:9])
list(continent_as.columns[1:9])
list(continent_eu.columns[1:9])


continent_af = continent_af.drop(columns=continent_af.columns[6:])
continent_af.to_csv('mod_continent_af.csv')

continent_as= continent_as.drop(columns=continent_as.columns[6:])
continent_as.to_csv('mod_continent_as.csv')

continent_eu = continent_eu.drop(columns=continent_eu.columns[6:])
continent_eu.to_csv('mod_continent_eu.csv')
#line of code drop colums 7 and onward 
continent_af['result'] = continent_af['total_cases'].div(continent_af['population']) * 100
print(continent_af)

continent_as['result'] = continent_as['total_cases'].div(continent_as['population']) * 100
print(continent_as)

continent_eu['result'] = continent_eu['total_cases'].div(continent_eu['population']) * 100
print(continent_eu)
#//Covers the percntage of population that was infected with covid//


continent_af = continent_af.sort_values('result', ascending=False)
continent_as = continent_as.sort_values('result', ascending=False)
continent_eu = continent_eu.sort_values('result', ascending=False)
# sorts the greatest percantage of a givinen populations infection's 

mod_continent_af = continent_af['total_cases'].sum()
df.loc[row_index, 'sum_oftotal_cases'] = mod_continent_af
print(mod_continent_af)
continent_af.to_csv('mod_continent_af.csv', index=False)

mod_continent_as = continent_as['total_cases'].sum()
df.loc[row_index, 'sum_oftotal_cases'] = sum_b
print(mod_continent_as)

#This code sum up the total cases for continet_af.csv

average_value = df['population'].mean()
print(average_value)
# This code finds the average population.

average_total_cases = df['total_cases'].mean()
print(average_total_cases)
#This code find the average number of total cases. 


filtered_df = df.query('new_cases > 500')
print(filtered_df)
filtered_df.to_csv('mod_continent')
#This code is a query that filters all the time total cases were above 500. 


filtered_cases = df[df['total_cases'] > 804676]
filtered_population = df[df['population'] > 127593486]
concatenated_df = pd.concat([filtered_cases, filtered_population])
filtered_con_pop_cases = concatenated_df[['iso_code', 'continent', 'location', 'total_cases', 'population']]
filtered_con_pop_cases.to_csv('filtered_con_pop_cases.csv', index=False)

continent_af['total_of_all_cases'] = continent_af['total_cases'].sum()
continent_af.to_csv('mod_continent_af.csv', index=False)



continent_af.to_csv('mod_continent_af.csv', index=False)
continent_as.to_csv('mod_continent_as.csv', index=False)
continent_eu.to_csv('mod_continent_eu.csv', index=False)


