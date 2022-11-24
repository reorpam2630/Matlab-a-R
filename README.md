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
