# Dimensionality reduction 

1. Principal component analysis (PCA): linear method  
2. Fisher's linear discriminant (FLD): linear method with group label information 
3. t-Distributed Stochastic Neighbor Embedding (tSNE): Nonlinear method 


## Example 1. IRIS data 

```Matlab 
% Dimensionality reduction 
clear all; 

% Load dataset 
load fisheriris; 
[n,p] = size(meas); 

figure; 
% Plot original data 
subplot(2,2,1); 
gscatter(meas(:,1),meas(:,2),species); 
title('Original data'); 
set(gca,'FontSize',14); 

% Linear method 
%%% Principal component analysis (PCA) 
% Centering 
X = meas - repmat(mean(meas,1),[n 1]); 
% Covariance matrix 
C = cov(X);
% Eigendecomposition of C 
[U,D] = eig(C); 
% Eigenvalues 
D = diag(D);
% Sort eigenvectors in the descending order of eigenvalues 
[tval,tind] = sort(D,'descend'); 
U = U(:,tind); 
D = tval;
% Projection to the principal components 
Y = X*U(:,1:2);

% Plot the results of PCA  
subplot(2,2,2); 
gscatter(Y(:,1),Y(:,2),species); 
title('PCA'); 
set(gca,'FontSize',14); 


% Linear method using group label information 
%%% Fisher's linear discriminant
% Number of groups 
group_label=unique(species)';
k=numel(group_label);

% Mean of all data 
M = mean(X);
% Within-scatter matrix 
Sw = 0;
% Between-scatter matrix 
Sb = 0;
for j = 1:k, 
    % Find the group j 
    tind = strcmp(group_label{j},species); 
    Xj = X(tind,:); 
    
    % Number of data in a group 
    nj = size(Xj,1); 
    % Mean of data in a group  
    Mj = mean(Xj);
    % Covariance matrix of data in a group 
    Cj = (Xj - repmat(Mj,[nj 1]))'*(Xj - repmat(Mj,[nj 1]));
    
    % Update within-scatter matrix 
    Sw = Sw + Cj; 
    % Update between-scatter matrix
    Sb = Sb + nj*(Mj-M)'*(Mj-M); 
end

% Generalized eigenvalue decomposition 
[U,D]=eig(Sb,Sw);
% Eigenvalues 
D=diag(D);
% Sort eigenvectors in the descending order of eigenvalues 
[tval,tind]=sort(D,'descend');
U = U(:,tind);

% Projection to discriminative hyperplane 
Y=X*U(:,1:2);

% Plot the results of FLD
subplot(2,2,3); 
gscatter(Y(:,1),Y(:,2),species); 
title('FLD'); 
set(gca,'FontSize',14); 


% Nonlinear method 
%%% t-Distributed Stochastic Neighbor Embedding (tSNE) 
rng default % for reproducibility
Y = tsne(meas);

% Plot the results of tSNE
subplot(2,2,4); 
gscatter(Y(:,1),Y(:,2),species); 
title('tSNE'); 
set(gca,'FontSize',14); 
```

![dimensionality_reduction_fisheriris](https://user-images.githubusercontent.com/54297018/63374120-24357400-c3c4-11e9-9572-9b901f4ce8dd.png)


## Example 2. MNIST data 

You can find MATLAB examples [here](https://www.mathworks.com/help/stats/visualize-high-dimensional-data-using-t-sne.html). 

```Matlab 
% Dimensionality reduction 
clear all; 

% Load dataset 
imageFileName = 't10k-images.idx3-ubyte';
labelFileName = 't10k-labels.idx1-ubyte';
% Please check your directory that has 'processMNISTdata.m'
% More friendly explanations can be found here https://www.mathworks.com/help/stats/visualize-high-dimensional-data-using-t-sne.html
addpath('C:\Users\user\Documents\MATLAB\Examples\stats\VisualizeHighDimensionalDataUsingTSNEExample'); % add to the path
[X,L] = processMNISTdata(imageFileName,labelFileName);
rmpath('C:\Users\user\Documents\MATLAB\Examples\stats\VisualizeHighDimensionalDataUsingTSNEExample'); % remove from the path
% X is a data matrix and L is a group label.  

% Number of data samples, dimension of data 
[n,p] = size(X); 

figure; 
% Linear method 
%%% Principal component analysis (PCA) 
% Centering 
X = X - repmat(mean(X,1),[n 1]); 
% Covariance matrix 
C = cov(X);
% Eigendecomposition of C 
[U,D] = eig(C); 
% Eigenvalues 
D = diag(D);
% Sort eigenvectors in the descending order of eigenvalues 
[tval,tind] = sort(D,'descend'); 
U = U(:,tind); 
D = tval;
% Projection to the principal components 
Y = X*U(:,1:2);

% Plot the results of PCA  
subplot(1,2,1); 
gscatter(Y(:,1),Y(:,2),L); 
title('PCA'); 
set(gca,'FontSize',14); 


% Nonlinear method 
%%% t-Distributed Stochastic Neighbor Embedding (tSNE) 
rng default % for reproducibility
Y = tsne(X,'Algorithm','barneshut','NumPCAComponents',50);

% Plot the results of tSNE
subplot(1,2,2); 
gscatter(Y(:,1),Y(:,2),L); 
title('tSNE'); 
set(gca,'FontSize',14); 
```

![dimensionality_reduction_mnist](https://user-images.githubusercontent.com/54297018/63375514-e423c080-c3c6-11e9-834d-97fa20886f87.png)

