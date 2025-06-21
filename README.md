# enso-eof-demo

🌊 El Niño detection and SST pattern analysis using EOFs in MATLAB with Climate Data Toolbox by Chad Green

This project uses MATLAB and the Climate Data Toolbox to analyze sea surface temperature (SST) anomalies and detect El Niño events using Empirical Orthogonal Function (EOF) analysis. It follows a clean, step-by-step structure to reproduce figures similar to those in Messié & Chavez (2011).

🔧 What This Project Covers

Loads and inspects SST data from the Pacific Ocean

Visualizes ENSO time series and key El Niño events

Analyzes SST trends and dominant spatial patterns (EOFs)

Reconstructs SST anomalies using EOF modes

Calculates and maps the Niño 3.4 index region anomalies

Animates SST anomaly evolution

📂 File Overview

pacific_sst.mat: Input dataset (SST, lat, lon, time)

enso_analysis.m: Script running all steps of the workflow

Uses Climate Data Toolbox functions such as enso, eof, trend, reof, anomaly, etc.

🧪 Method Workflow (Code Summary)

% Load SST data and check dimensions/time resolution
load pacific_sst.mat
whos
datestr(t([1 end]))
mean(diff(t))

% Grid latitude and longitude
[Lon,Lat] = meshgrid(lon,lat);

% ENSO Index and Time Series
idx = enso(sst,t,Lat,Lon);
anomaly(t,idx)
text(t([396 575 792]),idx([396 575 792]),'El Nino!','horiz','center','vert','bot')
datetick

% Power spectral density of ENSO index
plotpsd(idx,12,'logx','lambda')
xlabel 'periodicity (years)'
set(gca,'xtick',[1:7 33])

% Mean SST and Trend
sst_mean = mean(sst,3);
imagescn(lon,lat,sst_mean);
cmocean thermal

sst_trend = 365.25 * trend(sst,t,3);
imagescn(lon,lat,10*sst_trend);
cmocean('balance','pivot')

% EOF Analysis
imagescn(lon,lat,eof(sst,1))
hold on; plot(lon(12),lat(10),'ks')

sst1 = squeeze(sst(10,12,:));
plot(t,sst1); datetick

% Deseason and Detrend
sst1_ds = deseason(sst1,t);
sst_ds = deseason(sst,t);
sst_ds_dt = detrend3(sst_ds);

% Variance and EOFs
sst_anom_var = var(sst_ds_dt,[],3);
imagescn(lon,lat,sst_anom_var); colormap(jet); caxis([0 1])

[eof_maps,pc,expv] = eof(sst_ds_dt,6);

% Visualize EOF Maps and PCs
subplot(3,2,1); imagescn(lon,lat,eof_maps(:,:,1));
subplot(3,2,2); plot(t,pc(1,:));
...
sgtitle 'The first three principal components'

% Reconstruct SST Anomaly
subplot(1,2,1); imagescn(lon,lat,sst_ds_dt(:,:,1));
subplot(1,2,2); imagescn(lon,lat,eof_maps(:,:,1)*pc(1,1));
sgtitle(datestr(t(1),'yyyy-mmm-dd'))

% Composite reconstruction using first 6 EOFs
h2.CData = eof_maps(:,:,1)*pc(1,1) + ...
             eof_maps(:,:,6)*pc(6,1);

sst_ds_dt_r = reof(eof_maps,pc,1:5);

% Animate Reconstruction
for k = 1:120
   h1.CData = sst_ds_dt(:,:,k);
   h2.CData = sst_ds_dt_r(:,:,k);
   pause(0.1);
end

% Niño 3.4 Mask and Index
mask = geomask(Lat,Lon,[-5 5],[-170 -120]);
A = cdtarea(Lat,Lon);
sst_anom_34 = local(sst_ds_dt,mask,'weight',A);
anomaly(t,sst_anom_34,'color','none'); ylabel 'sst anomaly °C'

% Overlay ENSO and PC1
plot(t,idx,'k');
plot(idx,pc(1,:),'.');
polyplot(idx,pc(1,:))
pc1_scale = trend(idx,pc(1,:));
plot(t,pc1_scale*pc(1,:),'color',rgb('gold'),'linewidth',2)

📦 Toolbox Dependencies

This project uses functions from:

Climate Data Toolbox for MATLAB

Key functions:

enso, eof, reof, trend, anomaly, plotpsd, geomask, cdtarea, local, deseason, detrend3

📚 Reference

Messié, M. & Chavez, F. P. (2011). A global analysis of ENSO synchrony: The oceans’ seasonal footprint. Journal of Climate, 24(13), 3463–3477. https://doi.org/10.1175/2011JCLI3941.1

👤 Author

Your NameGitHub: @ameyagumaste Email: ameyagumaste@gmail.com

🌟 How to Support

⭐ Star this repo

📢 Share with others in climate science or data analysis

📬 Fork or suggest improvements

Credits: Chad Greene, GITHUB: https://github.com/chadagreene/CDT 
