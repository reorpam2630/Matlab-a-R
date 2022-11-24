# Codigo matlab 
function [promedioEscalogrma1,TiempoEscalograma,escalograma1,frecuencias,senfilt,tiempo] = FFTyWaveletParaChoherencias1canal2(PasAlts,PasBjs,WPasAlts,WPasBjs,nombre,canal)
load(nombre);%poner nombre del archivo
ganancia=10000;%% poner el valor de amplificacion
someData2=(someData2/ganancia)*1e6;
tabla=array2table ([someData2 someData]);%Aqui se pone los canales a utilizar.
number=height(tabla(:,1));%% cuenta el nuumero de canales
muestra.fsample=2000;%poner Frec de muestreo en Hz
time=1/muestra.fsample:1/muestra.fsample:number/muestra.fsample;
muestra.sampleinfo=[1 length(time)];%% cuenta el numero de datos
datos=table2array(tabla);
muestra.trial{1}=datos';
muestra.time{1}=time;
muestra.label={'1','2'};%%%%%%poner el nombre de los canales
Q=muestra.trial{1,1}(:,:);
Q(isnan(Q))=0;
muestra.trial{1,1}(:,:)=Q;
%% filtra la señal
cfg = [];
cfg.lpfilter = 'yes';
cfg.lpfreq = PasBjs;%filtro pasa bajas
cfg.lpfiltord =4;
cfg.hpfilter = 'yes';
cfg.hpfreq = PasAlts;%filtro pasa altas
cfg.hpfiltord =4;
 cfg.demean        = 'yes';
 cfg.dftfilter     = 'yes';
  cfg.dftreplace    = 'zero' ;
muestra = ft_preprocessing(cfg,muestra);%preprocesa la señal
muestra = ft_struct2single(muestra);
%% cambia frecuencia de muestreo
cfg=[];
cfg.resamplefs = 100;
muestra = ft_resampledata(cfg, muestra);
%%
figure (1)
 plot(muestra.time{1,1},muestra.trial{1,1}(2,:),'k','LineWidth',.5);
 legend('canal 1')
xlabel('Time (s)')
ylabel ('Amplitud norm.');
box off
grid off
set(gcf,'color','w')
set(findall(gcf,'-property','FontSize'),'FontSize',17)
% %

TI=input('Teimpo de interes');

% %%
cfg=[];
cfg.latency=TI;
muestra = ft_selectdata(cfg, muestra);
%% con lo siguiente se obtine el escalograma de todo el registro
for i=1:1:length(muestra.trial{1,1}(:,1))
muestra.trial{1,1}(i,:)=rescale(muestra.trial{1,1}(i,:),-1,1);
end
senfilt=muestra.trial{1,1};
cfg = [];
cfg.method     = 'wavelet';
cfg.width      = 6;
cfg.output     = 'pow' ;
cfg.foi = WPasAlts:.01:WPasBjs; % %rango de frecuencias para analizar
cfg.toi=muestra.time{1,1}(1):.01:muestra.time{1,1}(end);
freq = ft_freqanalysis(cfg,muestra);
escalograma1 = squeeze(freq.powspctrm(2,:,:));
frecuencias=freq.freq;
tiempo=muestra.time{1,1};
TiempoEscalograma=freq.time;
reescalado=rescale(escalograma1,0,1);

[escaloerrinf1,escaloerrsup1,promedioEscalogrma1,err1] = calcPromError(reescalado);

######################################################################################################

clear all
close all

%% intrucciones
%% [promedioEscalogrma1,TiempoEscalograma,escalograma1,frecuencias,senfilt,tiempo] =FFTyWaveletParaChoherencias1canal...
%%(PasAlts,PasBjs,WFrecInferior,WFrecSuperior,nombre,canal)
%PasAlts: filtro pasa altas
%PasBjs: filtr pasa bajas
%WFrecInferior: Frecuencia Inferior a analizar
%WFrecSuperior: Frecuencia superior a analizar
%nombre: nombre del archivo a abrir

load('Rata3EspontaneoODMesox10000.mat');%poner nombre del archivo

[promedioEscalogrma1,TiempoEscalograma,escalograma1,frecuencias,senfilt,tiempo] ...
    =FFTyWaveletParaChoherencias1canal2(.1,2.2,.1,10,nombre,[1]);
figure (2)
plot(frecuencias,promedioEscalogrma1);
ylabel('Potencia');
xlabel('Frecuencia (Hz)');
return
%restoredefaultpath
%addpath 'C:\Users\hp\AppData\Roaming\MathWorks\MATLAB Add-Ons\Collections\FieldTrip'
%ft_defaults






##Resultado
![Imagen1](https://user-images.githubusercontent.com/119010566/203870385-cac15357-8703-4b43-87a4-f11792aa4746.jpg)

#############################################################################################
clear all
close all

%% intrucciones
%% [promedioEscalogrma1,TiempoEscalograma,escalograma1,frecuencias,senfilt,tiempo] =FFTyWaveletParaChoherencias1canal...
%%(PasAlts,PasBjs,WFrecInferior,WFrecSuperior,nombre,canal)
%PasAlts: filtro pasa altas
%PasBjs: filtr pasa bajas
%WFrecInferior: Frecuencia Inferior a analizar
%WFrecSuperior: Frecuencia superior a analizar
%nombre: nombre del archivo a abrir

load('Rata3EspontaneoODMesox10000.mat');%poner nombre del archivo

[promedioEscalogrma1,TiempoEscalograma,escalograma1,frecuencias,senfilt,tiempo] ...
    =FFTyWaveletParaChoherencias1canal2(.1,10,.1,10,nombre,[1]);
figure (2)
plot(frecuencias,promedioEscalogrma1);
ylabel('Potencia');
xlabel('Frecuencia (Hz)');
figure (3)
surf(TiempoEscalograma,frecuencias,escalograma1,'EdgeColor','none'); %crea la grafica para el espectrograma
colorbar
xlabel('Tiempo (S)');
ylabel('Frecuencia (Hz)');
set(gca,'FontSize',15);
axis xy; axis tight; poder=colormap(parula); view(0,90);
set(gcf,'color','w');

return

%restoredefaultpath
%addpath 'C:\Users\hp\AppData\Roaming\MathWorks\MATLAB Add-Ons\Collections\FieldTrip'
%ft_defaults



####Resultado
![Imagen2](https://user-images.githubusercontent.com/119010566/203870544-e8113db2-496f-4774-8ea4-26cb9cdc5d3c.jpg)

#####################################################################################################################

 function [promedioEscalogrma1,promedioEscalogrma2,TiempoEscalograma,escalograma1,escalograma2,frecuencias,senfilt,tiempo] = FFTyWaveletParaChoherencias2canalesPamela(PasAlts,PasBjs,WPasAlts,WPasBjs,nombre,canal,TI)
load(nombre,'muestra');

%% filtra la señal
cfg = [];
cfg.lpfilter = 'yes';
cfg.lpfreq = PasBjs;%filtro pasa bajas
cfg.lpfiltord =5;
cfg.hpfilter = 'yes';
cfg.hpfreq = PasAlts;%filtro pasa altas
cfg.hpfiltord =4;
 cfg.demean        = 'yes';
 cfg.dftfilter     = 'yes';
  cfg.dftreplace    = 'zero' ;
muestra = ft_preprocessing(cfg,muestra);%preprocesa la señal
muestra = ft_struct2single(muestra);
%% cambia frecuencia de muestreo
cfg=[];
cfg.resamplefs = 50;
muestra = ft_resampledata(cfg, muestra);
%
cfg=[];
cfg.channel=canal;
cfg.latency     = TI;
muestra = ft_selectdata(cfg, muestra);

%% con lo siguiente se obtine el escalograma de todo el registro
muestra.trial{1,1}(1,:)=rescale(muestra.trial{1,1}(1,:),-1,1);
muestra.trial{1,1}(2,:)=rescale(muestra.trial{1,1}(2,:),-1,1);
senfilt=muestra.trial{1,1};
cfg = [];
cfg.method     = 'wavelet';
cfg.width      = 6;
cfg.output     = 'pow' ;
cfg.foi = WPasAlts:.01:WPasBjs; % %rango de frecuencias para analizar
cfg.toi=muestra.time{1,1}(1):.01:muestra.time{1,1}(end);
freq = ft_freqanalysis(cfg,muestra );
escalograma1 = squeeze(freq.powspctrm(1,:,:));
escalograma2 = squeeze(freq.powspctrm(2,:,:));
frecuencias=freq.freq;
tiempo=muestra.time{1,1};
TiempoEscalograma=freq.time;

%% grafica el escalograma1
figure (1)
subplot(3,2,3);
surf(TiempoEscalograma,frecuencias,escalograma1,'EdgeColor','none'); %crea la grafica para el espectrograma
c=colorbar;
c.Label.String = 'Potencia';
xlabel('Tiempo (seg)');
ylabel('Frecuencia (Hz)');
set(gca,'zscale','log')
axis xy; axis tight; poder=colormap(parula); view(0,90);
caxis([0 5])
set(gcf,'color','w');
title('Escalograma canal 1')
ylim([WPasAlts WPasBjs])
set(gca,'TickDir','out');

%% grafica el escalograma2
subplot(3,2,5);
surf(TiempoEscalograma,frecuencias,escalograma2,'EdgeColor','none'); %crea la grafica para el espectrograma
c=colorbar;
c.Label.String = 'Potencia';
xlabel('Tiempo (seg)');
ylabel('Frecuencia (Hz)');
caxis ([0 1]);
set(gca,'zscale','log')
axis xy; axis tight; poder=colormap(parula); view(0,90);
set(gcf,'color','w');
title('Escalograma canal 2')
ylim([WPasAlts WPasBjs])
set(gca,'TickDir','out');

%% crea y grafica el espectro promedio del escalograma 1
[escaloerrinf1,escaloerrsup1,promedioEscalogrma1,err1] = calcPromError(escalograma1);
subplot(3,2,[4,6]);
plot(frecuencias',promedioEscalogrma1,'color','k','LineWidth',2);
lo1 = escaloerrinf1;
hi1 = escaloerrsup1;
hp = patch([frecuencias'; frecuencias(end:-1:1)'; frecuencias(1)'], [lo1; hi1(end:-1:1); lo1(1)], 'k');
hold on;
hl = line(frecuencias',promedioEscalogrma1);
set(hp, 'facecolor', [0,0,0], 'edgecolor', 'none');
set(hl, 'color', 'k');
alpha(.1)
hold on
set(gca,'TickDir','out');
%% crea y grafica el espectro promedio del escalograma2
[escaloerrinf2,escaloerrsup2,promedioEscalogrma2,err2] = calcPromError(escalograma2);
plot(frecuencias',promedioEscalogrma2,'color','b','LineWidth',2);
lo1 = escaloerrinf2;
hi1 = escaloerrsup2;
hp = patch([frecuencias'; frecuencias(end:-1:1)'; frecuencias(1)'], [lo1; hi1(end:-1:1); lo1(1)], 'b');
hold on;
set(gca,'TickDir','out');
hl = line(frecuencias,promedioEscalogrma2);
set(hp, 'facecolor', [0,0,1], 'edgecolor', 'none');
set(hl, 'color', 'b');
alpha(.1)
ylabel('Potencia Relativa');
xlabel('Frecuencia (Hz)');
ylim([0 inf])
xlim([WPasAlts WPasBjs])
grid off
box off
set(gcf,'color','w');
set(gca,'TickDir','out');
shg
%% grafica la señal filtrada de los dos canales
subplot(3,2,[1,2])
 plot(tiempo,senfilt(1,:)+2,'k','LineWidth',.5);
 hold on
 plot(tiempo,senfilt(2,:),'b','LineWidth',.5);
 legend('canal 1','canal 2')
xlabel('Time (s)')
ylabel ('Amplitud norm.');
box off
grid off
set(gcf,'color','w')
set(gca,'TickDir','out');
set(findall(gcf,'-property','FontSize'),'FontSize',17)

%% con lo siguiente se obtinen los valores complejos e imaginarios
senfilt=muestra.trial{1,1};
cfg = [];
cfg.method     = 'wavelet';
cfg.width      = 6;
cfg.output  ='powandcsd';
cfg.foi = WPasAlts:.01:WPasBjs; % %rango de frecuencias para analizar
cfg.toi=muestra.time{1,1}(1):.01:muestra.time{1,1}(end);
freq = ft_freqanalysis(cfg,muestra );

%% valores imaginarios de coherencia
cfg = [];
cfg.method  ='coh';
cfg.complex = 'imag';
cfg.output = 'imag';
stat = ft_connectivityanalysis(cfg, freq);
CoherenciaImaginaria = squeeze(stat.cohspctrm);

frecuencias=freq.freq;
tiempo=muestra.time{1,1};
TiempoEscalograma=freq.time;

%% grafica la coherencia imaginaria a lo largo del tiempo
figure (2)
subplot(2,2,1);
surf(TiempoEscalograma,frecuencias,CoherenciaImaginaria,'EdgeColor','none'); %crea la grafica para el espectrograma
c=colorbar;
c.Label.String = 'Coherencia Imaginaria';
xlabel('Tiempo (seg)');
ylabel('Frecuencia (Hz)');
caxis ([-1 1]);
mapacolor=[1 0 0 ; 1 .4 .4 ; 1 .6 .6 ; 1 .8 .8 ; 1 1 1 ; .8 .8 1 ; .6 .6 1 ;.4 .4 1; 0 0 1];
axis xy; axis tight; poder=colormap(jet); view(0,90);
% axis xy; axis tight; poder=colormap(mapacolor); view(0,90);
set(gcf,'color','w');
set(gca,'TickDir','out');
hold off
%% grafica la coherencia imaginaria promedio
subplot(2,2,2);
[escaloerrinf,escaloerrsup,promedioImagiaria,err] = calcPromError(CoherenciaImaginaria);
plot(frecuencias',promedioImagiaria,'color','b','LineWidth',2);
lo1 = escaloerrinf;
hi1 = escaloerrsup;
hp = patch([frecuencias'; frecuencias(end:-1:1)'; frecuencias(1)'], [lo1; hi1(end:-1:1); lo1(1)], 'b');
hold on;
hl = line(frecuencias,promedioImagiaria);
set(hp, 'facecolor', [0,0,1], 'edgecolor', 'none');
set(hl, 'color', 'b');
alpha(.1)
ylabel('Coherencia Imaginaria');
xlabel('Frecuencia (Hz)');
ylim([-1 1])
grid off
box off
set(gcf,'color','w');
set(gca,'TickDir','out');
shg

%% valores reales de coherencia
cfg=[];
cfg.method  = 'coh';
cfg.complex = 'real';
cfg.output = 'real';
stat = ft_connectivityanalysis(cfg, freq);
CoherenciaReal=squeeze(stat.cohspctrm);
CoherenciaReal=abs(CoherenciaReal);
%% grafica la coherencia real a lo largo del teimpo
subplot(2,2,3);
surf(TiempoEscalograma,frecuencias,CoherenciaReal,'EdgeColor','none'); %crea la grafica para el espectrograma
c=colorbar;
c.Label.String = 'Coherencia Rel';
xlabel('Tiempo (seg)');
ylabel('Frecuencia (Hz)');
caxis ([0 1]);
set(gca,'zscale','log')
mapacolorCpherenciaRea=[0 0 1; .2 0 .8 ;.4 0 .6;.6 0 .6; 1 .8 .8; 1 .6 .6 ;1 .4 .4;1 .2 .2; 1 0 0];
axis xy; axis tight; poder=colormap(jet); view(0,90);
set(gcf,'color','w');
set(gca,'TickDir','out');
%% grafica la coherencia real promedio
subplot(2,2,4);
[escaloerrinf,escaloerrsup,promedioReal,err] = calcPromError(CoherenciaReal);
plot(frecuencias',promedioReal,'color','b','LineWidth',2);
lo1 = escaloerrinf;
hi1 = escaloerrsup;
hp = patch([frecuencias'; frecuencias(end:-1:1)'; frecuencias(1)'], [lo1; hi1(end:-1:1); lo1(1)], 'b');
hold on;
hl = line(frecuencias,promedioReal);
set(hp, 'facecolor', [0,0,1], 'edgecolor', 'none');
set(hl, 'color', 'b');
alpha(.1)
ylabel('Coherencia Real');
ylim([0 1.1])
xlabel('Frecuencia (Hz)');
grid off
box off
set(gcf,'color','w');
set(gca,'TickDir','out');
set(findall(gcf,'-property','FontSize'),'FontSize',17)
shg
shg;
return

%% calcula las p parala coherencia real
ValoresPfrecReal=[];
[numRows,numCols] = size(CoherenciaReal)
for i=1:1:numRows
[h,p,ci,zval] = ztest(CoherenciaReal(i,:),1,10)
ValoresPfrecReal=[ValoresPfrecReal p];
end
%%calcula lo valores p para la coherencia imaginaria
ValoresPfrecIm=[];
[numRows,numCols] = size(CoherenciaImaginaria)
for i=1:1:numRows
[h,p,ci,zval] = ztest(CoherenciaImaginaria(i,:),0,10)
ValoresPfrecIm=[ValoresPfrecIm p];
end



clear all
close all

%% intrucciones
%% [escalograma,frecuencias,senfilt,tiempo] = FFTyWaveletALF(PasAlts,PasBjs,WPasAlts,WPasBjs,nombre,canal,TI)
%PasBjs: filtr pasa bajas
%PasAlts: filtro pasa altas
%nombre: nombre del archivo a abrir

%%
tic
[promedioEscalogrma1,promedioEscalogrma2,TiempoEscalograma,escalograma1,escalograma2,frecuencias,senfilt,tiempo] ...
    =FFTyWaveletParaChoherencias2canalesPamela(1,10,1,10,'FieldRaton1CambiosdeLuzCada2minOIIniciaEsco.mat',{'1','2'},[0 20]);
toc




####Resultado
![Imagen3](https://user-images.githubusercontent.com/119010566/203870718-59b4486d-7df2-44c1-9ca2-e91defe45db7.jpg)
![Imagen4](https://user-images.githubusercontent.com/119010566/203870721-bf787355-d0c5-4544-bfa9-562620a4b85e.jpg)


