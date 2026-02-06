% MATLAB Script: 3D Point Cloud Comparison with Delta Visualization
% This script can be run directly or called as a function

% Clear workspace when running as script
clear; clc; close all;

% Generate sample data
fprintf('Generating sample data...\n');
n = 80;

% Create first point cloud (sphere-like distribution)
theta = 2*pi*rand(n,1);
phi = acos(2*rand(n,1)-1);
r = 5 + randn(n,1)*0.5;

X1 = r .* sin(phi) .* cos(theta);
Y1 = r .* sin(phi) .* sin(theta);
Z1 = r .* cos(phi);

% Create second point cloud with some directional movement
movement_magnitude = 2;
X2 = X1 + randn(n,1) * movement_magnitude + 0.5;
Y2 = Y1 + randn(n,1) * movement_magnitude;
Z2 = Z1 + randn(n,1) * movement_magnitude - 0.3;

% Run the comparison
scatter3_delta_comparison(X1, Y1, Z1, X2, Y2, Z2);

%% Function Definition
function scatter3_delta_comparison(X1, Y1, Z1, X2, Y2, Z2)
% SCATTER3_DELTA_COMPARISON Compare two 3D point clouds and visualize movement
%
% INPUTS:
%   X1, Y1, Z1 - Coordinates of first point cloud
%   X2, Y2, Z2 - Coordinates of second point cloud (corresponding points)
%
% EXAMPLE USAGE:
%   n = 100;
%   X1 = randn(n,1) * 5;
%   Y1 = randn(n,1) * 5;
%   Z1 = randn(n,1) * 5;
%   X2 = X1 + randn(n,1) * 2;
%   Y2 = Y1 + randn(n,1) * 2;
%   Z2 = Z1 + randn(n,1) * 2;
%   scatter3_delta_comparison(X1, Y1, Z1, X2, Y2, Z2);

    % Validate inputs
    if length(X1) ~= length(X2) || length(Y1) ~= length(Y2) || length(Z1) ~= length(Z2)
        error('Point clouds must have the same number of points');
    end
    
    % Calculate movement delta (Euclidean distance)
    delta = sqrt((X2 - X1).^2 + (Y2 - Y1).^2 + (Z2 - Z1).^2);
    
    % Create main figure
    figure('Position', [100, 100, 1200, 800], 'Name', 'Point Cloud Comparison');
    
    %% Main comparison plot
    subplot(2, 2, [1, 2]);
    hold on;
    
    % Original points (transparent blue)
    scatter3(X1, Y1, Z1, 60, [0.2 0.4 0.8], 'o', 'filled', ...
             'MarkerFaceAlpha', 0.3, 'DisplayName', 'Original');
    
    % Moved points (colored by delta magnitude)
    scatter3(X2, Y2, Z2, 80, delta, 'filled', ...
             'MarkerFaceAlpha', 0.9, 'DisplayName', 'Moved');
    
    % Movement vectors
    n_points = length(X1);
    for i = 1:n_points
        plot3([X1(i) X2(i)], [Y1(i) Y2(i)], [Z1(i) Z2(i)], ...
              'k-', 'LineWidth', 0.5, 'Color', [0.3 0.3 0.3 0.4]);
    end
    
    colormap(jet);
    cb = colorbar;
    cb.Label.String = 'Movement Magnitude';
    caxis([0 max(delta)]);
    
    grid on;
    xlabel('X', 'FontSize', 12, 'FontWeight', 'bold');
    ylabel('Y', 'FontSize', 12, 'FontWeight', 'bold');
    zlabel('Z', 'FontSize', 12, 'FontWeight', 'bold');
    title('3D Point Movement with Delta Gradient', 'FontSize', 14);
    legend('Location', 'best');
    view(45, 30);
    axis equal;
    hold off;
    
    %% Delta histogram
    subplot(2, 2, 3);
    histogram(delta, 20, 'FaceColor', [0.3 0.6 0.9], 'EdgeColor', 'k');
    xlabel('Movement Magnitude', 'FontSize', 11);
    ylabel('Frequency', 'FontSize', 11);
    title('Distribution of Movement Deltas', 'FontSize', 12);
    grid on;
    
    % Add statistics text
    text_x = max(delta) * 0.6;
    text_y = max(histcounts(delta, 20)) * 0.8;
    stats_text = sprintf('Mean: %.3f\nStd: %.3f\nMax: %.3f\nMin: %.3f', ...
                         mean(delta), std(delta), max(delta), min(delta));
    text(text_x, text_y, stats_text, 'FontSize', 10, ...
         'BackgroundColor', 'white', 'EdgeColor', 'black');
    
    %% Top view (X-Y plane)
    subplot(2, 2, 4);
    hold on;
    scatter(X1, Y1, 40, [0.2 0.4 0.8], 'o', 'filled', 'MarkerFaceAlpha', 0.3);
    scatter(X2, Y2, 60, delta, 'filled', 'MarkerFaceAlpha', 0.9);
    
    % Draw movement arrows in 2D
    for i = 1:n_points
        plot([X1(i) X2(i)], [Y1(i) Y2(i)], 'k-', 'LineWidth', 0.5, ...
             'Color', [0.3 0.3 0.3 0.4]);
    end
    
    colormap(jet);
    colorbar;
    caxis([0 max(delta)]);
    
    xlabel('X', 'FontSize', 11);
    ylabel('Y', 'FontSize', 11);
    title('Top View (X-Y Plane)', 'FontSize', 12);
    grid on;
    axis equal;
    hold off;
    
    %% Print statistics to console
    fprintf('\n=== Point Cloud Movement Analysis ===\n');
    fprintf('Total points: %d\n', n_points);
    fprintf('Mean delta: %.4f\n', mean(delta));
    fprintf('Median delta: %.4f\n', median(delta));
    fprintf('Std deviation: %.4f\n', std(delta));
    fprintf('Max delta: %.4f\n', max(delta));
    fprintf('Min delta: %.4f\n', min(delta));
    fprintf('====================================\n\n');
    
end