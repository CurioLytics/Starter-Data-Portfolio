-- Overview of COVID cases and deaths  
select location, total_cases, total_cases  
from PortfolioProject..CovidDeaths  
order by 2,3;  

-- Percentage of COVID cases relative to population (focus on Vietnam)  
select location,date, total_cases, population, total_cases*100.0/(population*1.0) as CasePercentage  
from PortfolioProject..CovidDeaths  
where location like '%viet%'  
order by 1,2;  

-- Country with the highest infection rate compared to population  
select location,max(total_cases) as Highestcases, population, cast(max(total_cases) as decimal)*100/cast(population as decimal) as percentOfInfected  
from PortfolioProject..CovidDeaths  
where continent is not null  
group by location,population  
order by 4 desc;  

-- Countries with the highest death rate compared to cases  
select location,max(total_cases) as Highestcases, max(total_deaths) as Deaths, max(total_deaths*100.0)/(max(total_cases)*1.0) as percentOfvictimDied  
from PortfolioProject..CovidDeaths  
where continent is not null  
group by location  
order by 4 desc;  

-- Total deaths by location  
select location, max(total_deaths) as HighestDeaths  
from PortfolioProject..CovidDeaths  
where continent is not null  
group by location  
order by 2 desc;  

-- Detailed view of COVID data  
select *   
from PortfolioProject..CovidDeaths  
where continent is not null  
order by 3,4;  

-- Broader observation of COVID data  

-- Continent with the highest infection rate  
select continent,max(total_deaths) as HighestDeaths  
from PortfolioProject..CovidDeaths  
where continent  is not null  
group by continent  
order by HighestDeaths desc;  

-- Daily global COVID metrics  
select date,sum(new_cases) as caseOfDay, sum(new_deaths) as deathsOfDay, cast(sum(new_deaths) as decimal)/cast(sum(new_cases) as decimal) *100 as VictimDiePerDay  
from PortfolioProject..CovidDeaths  
where continent is not null  
group by date  
order by 1,2;  

-- Population vs vaccination analysis  
-- Using CTE for rolling vaccination calculations  
with PopvsVac (Continent, Location,  Date, Population, new_vaccinations, RollingPeopleVaccinated)  
as  
(  
select dea.continent,dea.location, dea.date, dea.population, vac.new_vaccinations,  
       SUM(convert(int, vac.new_vaccinations)) OVER (Partition by dea.location order by  dea.date) as rollingPeopleVaccinated  
from PortfolioProject..CovidDeaths dea  
join  PortfolioProject..CovidVaccinations vac  
	on dea.location=vac.location  
	and dea.date=vac.date  
where dea.continent is not null  
)  
select *,(convert(decimal,rollingPeopleVaccinated)/cast(population as decimal))*100  as vaccinatedPercent  
from PopvsVac;  

-- Temporary table for population vaccination percentage  
drop table if exists #PercentPopulationVaccinated;  
create table #PercentPopulationVaccinated  
(  
continent nvarchar(50),  
location nvarchar(50),  
date date,  
population numeric,  
new_vaccinations numeric,  
RollingPeopleVaccinated numeric  
);  

insert into #PercentPopulationVaccinated  
select dea.continent,dea.location, dea.date, dea.population, vac.new_vaccinations,  
       SUM(convert(int, vac.new_vaccinations)) OVER (Partition by dea.location order by  dea.date) as rollingPeopleVaccinated  
from PortfolioProject..CovidDeaths dea  
join  PortfolioProject..CovidVaccinations vac  
	on dea.location=vac.location  
	and dea.date= vac.date  
where dea.continent is not null;  

select *,(convert(decimal,rollingPeopleVaccinated)/cast(population as decimal))*100  as vaccinatedPercent  
from #PercentPopulationVaccinated;  

-- Creating a view for future visualization  
create view PercentPopulationVaccinated as   
select dea.continent,dea.location, dea.date, dea.population, vac.new_vaccinations,  
       SUM(convert(int, vac.new_vaccinations)) OVER (Partition by dea.location order by  dea.date) as rollingPeopleVaccinated  
from PortfolioProject..CovidDeaths dea  
join  PortfolioProject..CovidVaccinations vac  
	on dea.location=vac.location  
	and dea.date= vac.date  
where dea.continent is not null;  

select *   
from PercentPopulationVaccinated;
