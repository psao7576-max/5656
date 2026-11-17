local Lighting = game:GetService("Lighting")
local Players = game:GetService("Players")
local Terrain = workspace.Terrain
local eu = Players.LocalPlayer

pcall(function()
    settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
    settings().Rendering.MeshPartDetailLevel = Enum.MeshPartDetailLevel.Level01
end)
pcall(function() setfpscap(60) end)
pcall(function() sethiddenproperty(Lighting, "Technology", Enum.Technology.Compatibility) end)

Lighting.GlobalShadows = false
Lighting.FogEnd = 9e9
Lighting.Brightness = 2
Lighting.EnvironmentDiffuseScale = 0
Lighting.EnvironmentSpecularScale = 0
Lighting.ShadowSoftness = 0

for _, v in ipairs(Lighting:GetChildren()) do
    pcall(function()
        if v:IsA("PostEffect") then v.Enabled = false
        elseif v:IsA("Atmosphere") or v:IsA("Sky") or v:IsA("Clouds") then v:Destroy() end
    end)
end

pcall(function()
    Terrain.WaterWaveSize = 0
    Terrain.WaterWaveSpeed = 0
    Terrain.WaterReflectance = 0
    Terrain.WaterTransparency = 1
    Terrain.Decoration = false
end)

local function otimizar(obj)
    pcall(function()
        if obj:IsA("BasePart") then
            obj.Material = Enum.Material.SmoothPlastic
            obj.Reflectance = 0
            obj.CastShadow = false
            if obj:IsA("MeshPart") then
                obj.RenderFidelity = Enum.RenderFidelity.Performance
                obj.TextureID = ""
            end
        elseif obj:IsA("Decal") or obj:IsA("Texture") or obj:IsA("SurfaceAppearance") then
            obj:Destroy()
        elseif obj:IsA("ParticleEmitter") or obj:IsA("Trail") or obj:IsA("Beam")
            or obj:IsA("Smoke") or obj:IsA("Fire") or obj:IsA("Sparkles") then
            obj.Enabled = false
        elseif obj:IsA("PointLight") or obj:IsA("SpotLight") or obj:IsA("SurfaceLight") then
            obj.Enabled = false
        elseif obj:IsA("PostEffect") then
            obj.Enabled = false
        elseif obj:IsA("Shirt") or obj:IsA("Pants") or obj:IsA("ShirtGraphic") or obj:IsA("Accessory") then
            local dono = obj:FindFirstAncestorOfClass("Model")
            if dono and dono ~= eu.Character then obj:Destroy() end
        end
    end)
end

task.spawn(function()
    local n = 0
    for _, obj in ipairs(workspace:GetDescendants()) do
        otimizar(obj)
        n += 1
        if n % 200 == 0 then task.wait() end
    end
end)

workspace.DescendantAdded:Connect(otimizar)

-- Limpeza de memória a cada 60s
task.spawn(function()
    while task.wait(60) do
        pcall(function() collectgarbage("collect") end)
    end
end)
