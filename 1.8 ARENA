-- language: Lua, target: 1.8 Arena, executor: Xeno
-- *Celestial 1.12.2 — Rayfield Gen2 GUI*
-- *KillAura | SpeedHack | ESP | Fly | TargetESP | Watermark*
-- *Trails | Chams | Blur | Target Strafe | China Hat*
-- *FOV Changer | Fullbright | Click Tp*

local Rayfield = loadstring(game:HttpGet("https://sirius.menu/gen2"))()

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Lighting = game:GetService("Lighting")

local LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera

-- ======================
-- НАСТРОЙКИ
-- ======================
local CONFIG = {
    KillAura = true,
    SpeedHack = true,
    ESP = true,
    Fly = false,
    TargetESP = true,
    Watermark = true,
    Trails = false,
    Chams = false,
    Blur = false,
    TargetStrafe = false,
    ChinaHat = false,
    FOVChanger = false,
    Fullbright = false,
    ClickTp = false,
    ESPColor = Color3.fromRGB(200, 100, 255),
    ESPFill = 0.5,
    ChamsColor = Color3.fromRGB(200, 100, 255),
    TrailColor = Color3.fromRGB(200, 100, 255),
    BlurSize = 10,
    StrafeRadius = 6.5,
    StrafeSpeed = 180,
    ChinaHatColor = Color3.fromRGB(255, 50, 50),
    FOVValue = 100,
    ClickTpKey = "E",
}

-- ======================
-- СОСТОЯНИЕ
-- ======================
local killauraEnabled = CONFIG.KillAura
local speedEnabled = CONFIG.SpeedHack
local espEnabled = CONFIG.ESP
local flyEnabled = CONFIG.Fly
local targetESPEnabled = CONFIG.TargetESP
local watermarkEnabled = CONFIG.Watermark
local trailsEnabled = CONFIG.Trails
local chamsEnabled = CONFIG.Chams
local blurEnabled = CONFIG.Blur
local targetStrafeEnabled = CONFIG.TargetStrafe
local chinaHatEnabled = CONFIG.ChinaHat
local fovEnabled = CONFIG.FOVChanger
local fullbrightEnabled = CONFIG.Fullbright
local clickTpEnabled = CONFIG.ClickTp
local targetESPMode = "по центру игрока"
local watermarkSide = "слева"
local watermarkSize = 13
local espColor = CONFIG.ESPColor
local espFill = CONFIG.ESPFill
local chamsColor = CONFIG.ChamsColor
local trailColor = CONFIG.TrailColor
local blurSize = CONFIG.BlurSize
local strafeRadius = CONFIG.StrafeRadius
local strafeSpeed = CONFIG.StrafeSpeed
local chinaHatColor = CONFIG.ChinaHatColor
local fovValue = CONFIG.FOVValue
local clickTpKey = CONFIG.ClickTpKey

local targetDist = 19.0
local speedBoost = 35.0
local flySpeed = 40.0
local lastHit = 0
local hitCooldown = 0.2

local currentFlyY = 0
local PlayerControls = nil
local espCache = {}
local window = nil
local targetMarker = nil
local targetScreenGui = nil
local currentTargetChar = nil

-- strafe
local strafeAngle = 0
local lastStrafeTarget = nil

-- watermark
local wmGui = nil
local wmHolder = nil
local wmHolderLayout = nil
local wmHeader = nil
local wmHeaderBar = nil
local wmHeaderLabel = nil
local wmListFrame = nil
local wmListLayout = nil
local wmModuleRows = {}
local wmMaxWidth = 0
local fpsCounter = 0
local fpsTime = 0
local currentFps = 0

-- trails
local trailObj = nil
local trailParticle = nil

-- chams
local chamsHighlights = {}

-- blur
local blurEffect = nil

-- china hat
local chinaHat = nil

-- fullbright
local savedLighting = nil
local fullbrightEffects = {}

-- ======================
-- ТЕМЫ
-- ======================
local THEMES = {
    ["Classic (красная)"] = {
        accent = Color3.fromRGB(220, 50, 50),
        esp = Color3.fromRGB(255, 0, 0),
        bg = Color3.fromRGB(20, 20, 20),
        button = Color3.fromRGB(220, 50, 50),
        toggle = Color3.fromRGB(220, 50, 50),
    },
    ["Celestial (фиолетовая)"] = {
        accent = Color3.fromRGB(200, 100, 255),
        esp = Color3.fromRGB(200, 100, 255),
        bg = Color3.fromRGB(18, 14, 28),
        button = Color3.fromRGB(200, 100, 255),
        toggle = Color3.fromRGB(200, 100, 255),
    },
    ["Ocean (синяя)"] = {
        accent = Color3.fromRGB(50, 130, 220),
        esp = Color3.fromRGB(50, 180, 255),
        bg = Color3.fromRGB(14, 18, 28),
        button = Color3.fromRGB(50, 130, 220),
        toggle = Color3.fromRGB(50, 130, 220),
    },
    ["Matrix (зелёная)"] = {
        accent = Color3.fromRGB(50, 200, 80),
        esp = Color3.fromRGB(0, 255, 100),
        bg = Color3.fromRGB(10, 18, 12),
        button = Color3.fromRGB(50, 200, 80),
        toggle = Color3.fromRGB(50, 200, 80),
    },
    ["Mono (серая)"] = {
        accent = Color3.fromRGB(150, 150, 150),
        esp = Color3.fromRGB(220, 220, 220),
        bg = Color3.fromRGB(18, 18, 18),
        button = Color3.fromRGB(150, 150, 150),
        toggle = Color3.fromRGB(150, 150, 150),
    },
}

-- ======================
-- ХЕЛПЕРЫ
-- ======================
local function getMyChar()
    return Workspace:FindFirstChild("LocalCharacter_" .. LocalPlayer.Name) or LocalPlayer.Character
end

local function getCurrentTime()
    local t = tick()
    local secs = math.floor(t % 86400)
    local h = math.floor(secs / 3600) % 24
    local m = math.floor(secs / 60) % 60
    local s = secs % 60
    return string.format("%02d:%02d:%02d", h, m, s)
end

local function keyFromString(str)
    if not str or str == "" then return Enum.KeyCode.E end
    local ok, key = pcall(function() return Enum.KeyCode[str] end)
    if ok then return key end
    return Enum.KeyCode.E
end

function applyESPColor()
    for char, _ in pairs(espCache) do
        if char and char.Parent then
            local hl = char:FindFirstChild("CelestialESP")
            if hl then
                hl.FillColor = espColor
                hl.OutlineColor = espColor
                hl.FillTransparency = espFill
            end
        end
    end

    if targetMarker then
        for _, d in ipairs(targetMarker:GetDescendants()) do
            if d:IsA("Frame") then d.BackgroundColor3 = espColor end
            if d:IsA("UIStroke") then d.Color = espColor end
        end
    end

    if targetScreenGui then
        for _, d in ipairs(targetScreenGui:GetDescendants()) do
            if d:IsA("Frame") then d.BackgroundColor3 = espColor end
            if d:IsA("UIStroke") then d.Color = espColor end
        end
    end

    if wmHeaderBar then wmHeaderBar.BackgroundColor3 = espColor end
    if wmHeaderLabel then wmHeaderLabel.TextColor3 = espColor end
    for _, row in ipairs(wmModuleRows) do
        if row.bar then row.bar.BackgroundColor3 = espColor end
    end
end

local function clearESP()
    for char, _ in pairs(espCache) do
        if char and char.Parent then
            local hl = char:FindFirstChild("CelestialESP")
            if hl then hl:Destroy() end
        end
        espCache[char] = nil
    end
end

-- ======================
-- TRAILS
-- ======================
local function destroyTrail()
    if trailObj then trailObj:Destroy() trailObj = nil end
    if trailParticle then trailParticle:Destroy() trailParticle = nil end
end

local function createTrail()
    destroyTrail()

    local myChar = getMyChar()
    if not myChar then return end

    local hitbox = myChar:FindFirstChild("PlayerHitbox")
    local torso = myChar:FindFirstChild("HeadTorso") or myChar:FindFirstChild("Torso")
    local base = hitbox or torso
    if not base then return end

    local att0 = Instance.new("Attachment")
    att0.Name = "CelestialTrailA0"
    att0.Position = Vector3.new(0, 1, 0)
    att0.Parent = base

    local att1 = Instance.new("Attachment")
    att1.Name = "CelestialTrailA1"
    att1.Position = Vector3.new(0, -1, 0)
    att1.Parent = base

    local trail = Instance.new("Trail")
    trail.Name = "CelestialTrail"
    trail.Attachment0 = att0
    trail.Attachment1 = att1
    trail.Lifetime = 1.2
    trail.MinLength = 0
    trail.FaceCamera = true
    trail.LightEmission = 1
    trail.LightInfluence = 0
    trail.Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, trailColor),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 255, 255)),
    })
    trail.Transparency = NumberSequence.new({
        NumberSequenceKeypoint.new(0, 0),
        NumberSequenceKeypoint.new(0.7, 0.4),
        NumberSequenceKeypoint.new(1, 1),
    })
    trail.WidthScale = NumberSequence.new({
        NumberSequenceKeypoint.new(0, 1),
        NumberSequenceKeypoint.new(1, 0),
    })
    trail.Parent = base

    local emitter = Instance.new("ParticleEmitter")
    emitter.Name = "CelestialTrailParticles"
    emitter.Texture = "rbxassetid://243660364"
    emitter.LightEmission = 1
    emitter.LightInfluence = 0
    emitter.Size = NumberSequence.new({
        NumberSequenceKeypoint.new(0, 0.6),
        NumberSequenceKeypoint.new(1, 0),
    })
    emitter.Transparency = NumberSequence.new({
        NumberSequenceKeypoint.new(0, 0.2),
        NumberSequenceKeypoint.new(1, 1),
    })
    emitter.Lifetime = NumberRange.new(0.6, 1.0)
    emitter.Rate = 40
    emitter.Speed = NumberRange.new(0.5, 1.5)
    emitter.SpreadAngle = Vector2.new(180, 180)
    emitter.Color = ColorSequence.new(trailColor)
    emitter.Parent = att0

    trailObj = trail
    trailParticle = emitter
end

local function updateTrailColor()
    if trailObj then
        trailObj.Color = ColorSequence.new({
            ColorSequenceKeypoint.new(0, trailColor),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 255, 255)),
        })
    end
    if trailParticle then
        trailParticle.Color = ColorSequence.new(trailColor)
    end
end

-- ======================
-- CHAMS
-- ======================
local CHAMS_BLACKLIST = {
    ["PlayerHitbox"] = true,
    ["HeadTorso"] = true,
    ["PlayerEyeLevel"] = true,
    ["HumanoidRootPart"] = true,
    ["Humanoid"] = true,
    ["Animate"] = true,
    ["Torso"] = true,
    ["Head"] = true,
    ["Left Arm"] = true,
    ["Right Arm"] = true,
    ["Left Leg"] = true,
    ["Right Leg"] = true,
    ["UpperTorso"] = true,
    ["LowerTorso"] = true,
    ["LeftUpperArm"] = true,
    ["LeftLowerArm"] = true,
    ["LeftHand"] = true,
    ["RightUpperArm"] = true,
    ["RightLowerArm"] = true,
    ["RightHand"] = true,
    ["LeftUpperLeg"] = true,
    ["LeftLowerLeg"] = true,
    ["LeftFoot"] = true,
    ["RightUpperLeg"] = true,
    ["RightLowerLeg"] = true,
    ["RightFoot"] = true,
}

local function clearChams()
    for obj, _ in pairs(chamsHighlights) do
        if obj and obj.Parent then
            for _, d in ipairs(obj:GetDescendants()) do
                if d.Name == "CelestialChams" or d.Name == "CelestialChamsBox" then
                    d:Destroy()
                end
            end
            local hl = obj:FindFirstChild("CelestialChams")
            if hl then hl:Destroy() end
            local sb = obj:FindFirstChild("CelestialChamsBox")
            if sb then sb:Destroy() end
        end
    end
    chamsHighlights = {}
end

local function applyChamsToObject(obj)
    if not obj or not obj.Parent then return end
    if obj:FindFirstChild("CelestialChams") then return end

    local hl = Instance.new("Highlight")
    hl.Name = "CelestialChams"
    hl.FillColor = chamsColor
    hl.FillTransparency = 0.2
    hl.OutlineColor = chamsColor
    hl.OutlineTransparency = 0
    hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    hl.Parent = obj

    if obj:IsA("Model") then
        for _, part in ipairs(obj:GetDescendants()) do
            if part:IsA("BasePart") and not part:FindFirstChild("CelestialChamsBox") then
                local box = Instance.new("SelectionBox")
                box.Name = "CelestialChamsBox"
                box.Adornee = part
                box.Color3 = chamsColor
                box.LineThickness = 0.02
                box.SurfaceTransparency = 0.4
                box.Parent = part
            end
        end
    elseif obj:IsA("BasePart") then
        if not obj:FindFirstChild("CelestialChamsBox") then
            local box = Instance.new("SelectionBox")
            box.Name = "CelestialChamsBox"
            box.Adornee = obj
            box.Color3 = chamsColor
            box.LineThickness = 0.02
            box.SurfaceTransparency = 0.4
            box.Parent = obj
        end
    end

    chamsHighlights[obj] = true
end

local function isWeaponOrProp(obj)
    if not obj or not obj.Parent then return false end
    if CHAMS_BLACKLIST[obj.Name] then return false end
    if obj.Name == "CelestialChams" or obj.Name == "CelestialChamsBox" then return false end
    if obj.Name == "CelestialESP" or obj.Name == "__vanta_esp" or obj.Name == "ESPHighlight" then return false end
    if obj.Name:sub(1, 14) == "CelestialTrail" then return false end
    if obj.Name:sub(1, 13) == "CelestialCham" then return false end
    if obj.Name:sub(1, 13) == "CelestialChin" then return false end

    if obj:IsA("Tool") then return true end

    if obj:IsA("Model") then
        if obj:FindFirstChild("Handle") then return true end
        local n = obj.Name:lower()
        if n:find("sword") or n:find("gun") or n:find("knife")
           or n:find("axe") or n:find("bow") or n:find("weapon")
           or n:find("tool") or n:find("melee") or n:find("staff")
           or n:find("spear") or n:find("blade") or n:find("hammer")
           or n:find("pistol") or n:find("rifle") or n:find("bat") then
            return true
        end
    end

    if obj:IsA("BasePart") or obj:IsA("MeshPart") then
        local n = obj.Name:lower()
        if n:find("sword") or n:find("gun") or n:find("knife")
           or n:find("axe") or n:find("bow") or n:find("weapon")
           or n:find("handle") or n:find("blade") or n:find("staff")
           or n:find("hammer") or n:find("spear") then
            return true
        end
    end

    return false
end

local function updateChams()
    if not chamsEnabled then
        if next(chamsHighlights) then clearChams() end
        return
    end

    local myChar = getMyChar()
    if myChar then
        for _, obj in ipairs(myChar:GetDescendants()) do
            if isWeaponOrProp(obj) then
                applyChamsToObject(obj)
            end
        end
        if myChar:IsA("Model") and not chamsHighlights[myChar] then
            applyChamsToObject(myChar)
        end
    end

    local otherChars = Workspace:FindFirstChild("OtherCharacters")
    if otherChars then
        for _, char in ipairs(otherChars:GetChildren()) do
            for _, obj in ipairs(char:GetDescendants()) do
                if isWeaponOrProp(obj) then
                    applyChamsToObject(obj)
                end
            end
        end
    end
end

local function updateChamsColor()
    for obj, _ in pairs(chamsHighlights) do
        if obj and obj.Parent then
            local hl = obj:FindFirstChild("CelestialChams")
            if hl then
                hl.FillColor = chamsColor
                hl.OutlineColor = chamsColor
            end
            for _, d in ipairs(obj:GetDescendants()) do
                if d:IsA("SelectionBox") and d.Name == "CelestialChamsBox" then
                    d.Color3 = chamsColor
                end
            end
        end
    end
end

-- ======================
-- BLUR
-- ======================
local function createBlur()
    if blurEffect then return end
    blurEffect = Instance.new("BlurEffect")
    blurEffect.Name = "CelestialBlur"
    blurEffect.Size = blurSize
    blurEffect.Parent = Lighting
end

local function destroyBlur()
    if blurEffect then blurEffect:Destroy() blurEffect = nil end
    for _, obj in ipairs(Lighting:GetChildren()) do
        if obj:IsA("BlurEffect") and obj.Name == "CelestialBlur" then
            obj:Destroy()
        end
    end
end

local function updateBlur()
    if blurEnabled then
        if not blurEffect then createBlur() end
        if blurEffect then blurEffect.Size = blurSize end
    else
        destroyBlur()
    end
end

-- ======================
-- CHINA HAT
-- ======================
local function destroyChinaHat()
    if not chinaHat then return end
    if chinaHat.part then chinaHat.part:Destroy() end
    if chinaHat.ring then chinaHat.ring:Destroy() end
    chinaHat = nil
end

local function createChinaHat()
    destroyChinaHat()

    local myChar = getMyChar()
    if not myChar then return end

    local head = myChar:FindFirstChild("Head")
        or myChar:FindFirstChild("HeadTorso")
        or myChar:FindFirstChild("Torso")
        or myChar:FindFirstChild("PlayerHitbox")
    if not head then return end

    local part = Instance.new("Part")
    part.Name = "CelestialChinaHat"
    part.Size = Vector3.new(3, 0.1, 3)
    part.Transparency = 0
    part.CanCollide = false
    part.CanQuery = false
    part.CanTouch = false
    part.Massless = true
    part.Anchored = false
    part.Color = chinaHatColor
    part.Material = Enum.Material.Neon
    part.CastShadow = false
    part.Parent = myChar

    part.CFrame = head.CFrame * CFrame.new(0, 2.2, 0)

    local weld = Instance.new("WeldConstraint")
    weld.Part0 = head
    weld.Part1 = part
    weld.Parent = part

    local mesh = Instance.new("SpecialMesh")
    mesh.Name = "HatMesh"
    mesh.MeshType = Enum.MeshType.FileMesh
    mesh.MeshId = "rbxassetid://1033714"
    mesh.Scale = Vector3.new(1.4, 1.6, 1.4)
    mesh.Parent = part

    local highlight = Instance.new("Highlight")
    highlight.Name = "CelestialChinaHatHL"
    highlight.Adornee = part
    highlight.FillColor = chinaHatColor
    highlight.FillTransparency = 0.15
    highlight.OutlineColor = chinaHatColor
    highlight.OutlineTransparency = 0
    highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    highlight.Parent = part

    local light = Instance.new("PointLight")
    light.Name = "HatLight"
    light.Color = chinaHatColor
    light.Brightness = 3
    light.Range = 12
    light.Shadows = false
    light.Parent = part

    local ring = Instance.new("Part")
    ring.Name = "CelestialChinaHatRing"
    ring.Shape = Enum.PartType.Cylinder
    ring.Size = Vector3.new(0.2, 3.2, 3.2)
    ring.Transparency = 0
    ring.CanCollide = false
    ring.CanQuery = false
    ring.CanTouch = false
    ring.Massless = true
    ring.Anchored = false
    ring.Color = chinaHatColor
    ring.Material = Enum.Material.Neon
    ring.CastShadow = false
    ring.Parent = myChar

    ring.CFrame = head.CFrame * CFrame.new(0, 1.8, 0) * CFrame.Angles(0, 0, math.rad(90))

    local ringWeld = Instance.new("WeldConstraint")
    ringWeld.Part0 = head
    ringWeld.Part1 = ring
    ringWeld.Parent = ring

    local ringHighlight = Instance.new("Highlight")
    ringHighlight.Name = "CelestialChinaHatRingHL"
    ringHighlight.Adornee = ring
    ringHighlight.FillColor = chinaHatColor
    ringHighlight.FillTransparency = 0.1
    ringHighlight.OutlineColor = chinaHatColor
    ringHighlight.OutlineTransparency = 0
    ringHighlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    ringHighlight.Parent = ring

    chinaHat = {
        part = part,
        mesh = mesh,
        ring = ring,
        highlight = highlight,
        ringHighlight = ringHighlight,
        light = light,
    }
end

local function updateChinaHatColor()
    if not chinaHat then return end
    if chinaHat.part then chinaHat.part.Color = chinaHatColor end
    if chinaHat.ring then chinaHat.ring.Color = chinaHatColor end
    if chinaHat.highlight then
        chinaHat.highlight.FillColor = chinaHatColor
        chinaHat.highlight.OutlineColor = chinaHatColor
    end
    if chinaHat.ringHighlight then
        chinaHat.ringHighlight.FillColor = chinaHatColor
        chinaHat.ringHighlight.OutlineColor = chinaHatColor
    end
    if chinaHat.light then chinaHat.light.Color = chinaHatColor end
end

local function updateChinaHat()
    if not chinaHatEnabled then
        if chinaHat then destroyChinaHat() end
        return
    end

    local myChar = getMyChar()
    if not myChar then
        destroyChinaHat()
        return
    end

    if not chinaHat or not chinaHat.part or not chinaHat.part.Parent then
        createChinaHat()
    end
end

-- ======================
-- FOV CHANGER
-- ======================
local function updateFOV()
    if fovEnabled then
        Camera.FieldOfView = fovValue
    else
        -- возвращаем дефолтное значение Roblox
        Camera.FieldOfView = 70
    end
end

-- ======================
-- FULLBRIGHT
-- ======================
local function createFullbright()
    if savedLighting then return end

    savedLighting = {
        Ambient = Lighting.Ambient,
        OutdoorAmbient = Lighting.OutdoorAmbient,
        Brightness = Lighting.Brightness,
        GlobalShadows = Lighting.GlobalShadows,
        FogEnd = Lighting.FogEnd,
        FogStart = Lighting.FogStart,
        ClockTime = Lighting.ClockTime,
    }

    Lighting.Ambient = Color3.fromRGB(200, 200, 200)
    Lighting.OutdoorAmbient = Color3.fromRGB(200, 200, 200)
    Lighting.Brightness = 2
    Lighting.GlobalShadows = false
    Lighting.FogEnd = 100000
    Lighting.FogStart = 100000
    Lighting.ClockTime = 12

    -- удаляем все затемнения от эффектов
    for _, obj in ipairs(Lighting:GetChildren()) do
        if obj:IsA("Atmosphere") then
            obj.Density = 0
            obj.Haze = 0
        end
        if obj:IsA("ColorCorrectionEffect") then
            if not obj:FindFirstChild("CelestialFBMarker") then
                local marker = Instance.new("BoolValue")
                marker.Name = "CelestialFBMarker"
                marker.Parent = obj
                table.insert(fullbrightEffects, { obj = obj, brightness = obj.Brightness, contrast = obj.Contrast })
                obj.Brightness = 0.2
                obj.Contrast = 0
            end
        end
    end
end

local function destroyFullbright()
    if not savedLighting then return end

    Lighting.Ambient = savedLighting.Ambient
    Lighting.OutdoorAmbient = savedLighting.OutdoorAmbient
    Lighting.Brightness = savedLighting.Brightness
    Lighting.GlobalShadows = savedLighting.GlobalShadows
    Lighting.FogEnd = savedLighting.FogEnd
    Lighting.FogStart = savedLighting.FogStart
    Lighting.ClockTime = savedLighting.ClockTime

    for _, entry in ipairs(fullbrightEffects) do
        if entry.obj and entry.obj.Parent then
            entry.obj.Brightness = entry.brightness
            entry.obj.Contrast = entry.contrast
            local marker = entry.obj:FindFirstChild("CelestialFBMarker")
            if marker then marker:Destroy() end
        end
    end
    fullbrightEffects = {}
    savedLighting = nil
end

local function updateFullbright()
    if fullbrightEnabled then
        createFullbright()
    else
        destroyFullbright()
    end
end

-- ======================
-- CLICK TP
-- ======================
local function getPlayerInCrosshair()
    -- ищем игрока, ближайшего к центру экрана в пределах 500 studs
    local camPos = Camera.CFrame.Position
    local camLook = Camera.CFrame.LookVector

    local best = nil
    local bestDot = 0.9   -- минимальный косинус угла (примерно 25°)

    local otherChars = Workspace:FindFirstChild("OtherCharacters")
    if not otherChars then return nil end

    for _, char in ipairs(otherChars:GetChildren()) do
        local target = char:FindFirstChild("Torso")
            or char:FindFirstChild("HumanoidRootPart")
            or char:FindFirstChild("PlayerHitbox")
            or char:FindFirstChild("HeadTorso")
        if target then
            local toTarget = (target.Position - camPos)
            local dist = toTarget.Magnitude
            if dist > 0 and dist < 500 then
                local dir = toTarget.Unit
                local dot = camLook:Dot(dir)
                if dot > bestDot then
                    bestDot = dot
                    best = target
                end
            end
        end
    end
    return best
end

local function doClickTp()
    if not clickTpEnabled then return end

    local myChar = getMyChar()
    if not myChar then return end

    local hitbox = myChar:FindFirstChild("PlayerHitbox")
    if not hitbox then return end

    local target = getPlayerInCrosshair()
    if not target then return end

    -- телепорт: ставим свою позицию рядом с целью
    hitbox.CFrame = CFrame.new(target.Position + Vector3.new(0, 0, 3)) * hitbox.CFrame.Rotation
    hitbox.AssemblyLinearVelocity = Vector3.zero
end

-- ======================
-- TARGET STRAFE
-- ======================
local function applyTargetStrafe(target, dt)
    if not targetStrafeEnabled or not target then return end

    local myChar = getMyChar()
    if not myChar then return end

    local hitbox = myChar:FindFirstChild("PlayerHitbox")
    if not hitbox then return end

    local tRoot = target:FindFirstChild("Torso")
        or target:FindFirstChild("HumanoidRootPart")
        or target:FindFirstChild("PlayerHitbox")
        or target:FindFirstChild("HeadTorso")
    if not tRoot then return end

    if lastStrafeTarget ~= target then
        local currentPos = hitbox.Position
        local targetPos = tRoot.Position
        local offset = Vector3.new(currentPos.X - targetPos.X, 0, currentPos.Z - targetPos.Z)
        if offset.Magnitude > 0.1 then
            strafeAngle = math.atan2(offset.Z, offset.X)
        else
            strafeAngle = 0
        end
        lastStrafeTarget = target
    end

    local dir = 1
    if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) or UserInputService:IsKeyDown(Enum.KeyCode.RightShift) then
        dir = -1
    end

    strafeAngle = strafeAngle + math.rad(strafeSpeed) * dir * dt

    local targetPos = tRoot.Position
    local desiredX = targetPos.X + math.cos(strafeAngle) * strafeRadius
    local desiredZ = targetPos.Z + math.sin(strafeAngle) * strafeRadius

    local currentPos = hitbox.Position

    local desiredY = currentPos.Y
    local yDiff = targetPos.Y - currentPos.Y
    if math.abs(yDiff) > 0.5 then
        local ySpeed = 25
        local step = ySpeed * dt
        if yDiff > 0 then
            desiredY = currentPos.Y + math.min(step, yDiff)
        else
            desiredY = currentPos.Y - math.min(step, -yDiff)
        end
    end

    hitbox.CFrame = CFrame.new(desiredX, desiredY, desiredZ) * hitbox.CFrame.Rotation
end

-- ======================
-- WATERMARK
-- ======================
local function getActiveModules()
    local list = {}
    if killauraEnabled then table.insert(list, "KillAura") end
    if speedEnabled then table.insert(list, "SpeedHack") end
    if espEnabled then table.insert(list, "ESP") end
    if targetESPEnabled then table.insert(list, "TargetESP") end
    if flyEnabled then table.insert(list, "Fly") end
    if trailsEnabled then table.insert(list, "Trails") end
    if chamsEnabled then table.insert(list, "Chams") end
    if blurEnabled then table.insert(list, "Blur") end
    if targetStrafeEnabled then table.insert(list, "TargetStrafe") end
    if chinaHatEnabled then table.insert(list, "ChinaHat") end
    if fovEnabled then table.insert(list, "FOV") end
    if fullbrightEnabled then table.insert(list, "Fullbright") end
    if clickTpEnabled then table.insert(list, "ClickTp") end
    return list
end

local function recomputeWidth()
    if not wmHolder then return end
    local isRight = (watermarkSide == "справа")
    local maxW = 0

    if wmHeader then
        local w = wmHeader.AbsoluteSize.X
        if w > maxW then maxW = w end
    end
    for _, row in ipairs(wmModuleRows) do
        if row.frame then
            local w = row.frame.AbsoluteSize.X
            if w > maxW then maxW = w end
        end
    end
    if maxW < 80 then maxW = 80 end
    wmMaxWidth = maxW

    wmHolder.Size = UDim2.new(0, maxW, 0, 0)
    if wmListFrame then wmListFrame.Size = UDim2.new(0, maxW, 0, 0) end

    local align = isRight and Enum.HorizontalAlignment.Right or Enum.HorizontalAlignment.Left
    if wmListLayout then wmListLayout.HorizontalAlignment = align end
    if wmHolderLayout then wmHolderLayout.HorizontalAlignment = align end
end

local function applyRowOrientation(row)
    if not row or not row.frame then return end
    local isRight = (watermarkSide == "справа")
    local bar = row.bar
    local label = row.label
    local frame = row.frame

    if isRight then
        bar.AnchorPoint = Vector2.new(1, 0)
        bar.Position = UDim2.new(1, 0, 0, 0)
        label.AnchorPoint = Vector2.new(1, 0)
        label.Position = UDim2.new(1, -8, 0, 0)
        label.TextXAlignment = Enum.TextXAlignment.Right
    else
        bar.AnchorPoint = Vector2.new(0, 0)
        bar.Position = UDim2.new(0, 0, 0, 0)
        label.AnchorPoint = Vector2.new(0, 0)
        label.Position = UDim2.new(0, 8, 0, 0)
        label.TextXAlignment = Enum.TextXAlignment.Left
    end

    local w = label.AbsoluteSize.X + 20
    if w < 40 then w = 40 end
    frame.Size = UDim2.new(0, w, 0, watermarkSize + 6)
end

local function applyHeaderOrientation()
    if not wmHeaderLabel or not wmHeaderBar then return end
    local isRight = (watermarkSide == "справа")

    if isRight then
        wmHeaderBar.AnchorPoint = Vector2.new(1, 0)
        wmHeaderBar.Position = UDim2.new(1, 0, 0, 0)
        wmHeaderLabel.AnchorPoint = Vector2.new(1, 0)
        wmHeaderLabel.Position = UDim2.new(1, -8, 0, 0)
        wmHeaderLabel.TextXAlignment = Enum.TextXAlignment.Right
    else
        wmHeaderBar.AnchorPoint = Vector2.new(0, 0)
        wmHeaderBar.Position = UDim2.new(0, 0, 0, 0)
        wmHeaderLabel.AnchorPoint = Vector2.new(0, 0)
        wmHeaderLabel.Position = UDim2.new(0, 8, 0, 0)
        wmHeaderLabel.TextXAlignment = Enum.TextXAlignment.Left
    end
end

local function applyWatermarkLayout()
    if not wmHolder then return end

    if watermarkSide == "справа" then
        wmHolder.AnchorPoint = Vector2.new(1, 0)
        wmHolder.Position = UDim2.new(1, -6, 0, 6)
    else
        wmHolder.AnchorPoint = Vector2.new(0, 0)
        wmHolder.Position = UDim2.new(0, 6, 0, 6)
    end

    if wmHeaderLabel then wmHeaderLabel.TextSize = watermarkSize + 2 end
    for _, row in ipairs(wmModuleRows) do
        if row.label then row.label.TextSize = watermarkSize end
    end

    task.defer(function()
        applyHeaderOrientation()
        for _, row in ipairs(wmModuleRows) do
            applyRowOrientation(row)
        end
        task.defer(recomputeWidth)
    end)
end

local function createModuleRow(parent, text, index)
    local row = Instance.new("Frame")
    row.Name = "Row_" .. text
    row.BackgroundColor3 = Color3.fromRGB(28, 28, 36)
    row.BackgroundTransparency = 0.15
    row.BorderSizePixel = 0
    row.Size = UDim2.new(0, 100, 0, watermarkSize + 6)
    row.LayoutOrder = index

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 4)
    corner.Parent = row

    local bar = Instance.new("Frame")
    bar.Name = "Bar"
    bar.BackgroundColor3 = espColor
    bar.BorderSizePixel = 0
    bar.Size = UDim2.new(0, 2, 1, 0)
    bar.ZIndex = 2
    bar.Parent = row

    local barCorner = Instance.new("UICorner")
    barCorner.CornerRadius = UDim.new(0, 4)
    barCorner.Parent = bar

    local label = Instance.new("TextLabel")
    label.Name = "Label"
    label.BackgroundTransparency = 1
    label.Font = Enum.Font.Code
    label.TextSize = watermarkSize
    label.TextColor3 = Color3.fromRGB(255, 255, 255)
    label.Text = text
    label.Size = UDim2.new(0, 0, 1, 0)
    label.AutomaticSize = Enum.AutomaticSize.X
    label.ZIndex = 3
    label.Parent = row

    local entry = { frame = row, label = label, bar = bar }

    label:GetPropertyChangedSignal("AbsoluteSize"):Connect(function()
        if not row or not row.Parent then return end
        local w = label.AbsoluteSize.X + 20
        if w < 40 then w = 40 end
        row.Size = UDim2.new(0, w, 0, watermarkSize + 6)
        task.defer(recomputeWidth)
    end)

    applyRowOrientation(entry)
    row.Parent = parent
    return entry
end

local function createWatermark()
    if wmGui then return end

    wmGui = Instance.new("ScreenGui")
    wmGui.Name = "CelestialWatermark"
    wmGui.IgnoreGuiInset = true
    wmGui.ResetOnSpawn = false
    wmGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    wmGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

    wmHolder = Instance.new("Frame")
    wmHolder.Name = "Holder"
    wmHolder.BackgroundTransparency = 1
    wmHolder.Size = UDim2.new(0, 200, 0, 0)
    wmHolder.AutomaticSize = Enum.AutomaticSize.Y
    wmHolder.ZIndex = 3
    wmHolder.Parent = wmGui

    wmHolderLayout = Instance.new("UIListLayout")
    wmHolderLayout.SortOrder = Enum.SortOrder.LayoutOrder
    wmHolderLayout.Padding = UDim.new(0, 2)
    wmHolderLayout.HorizontalAlignment = Enum.HorizontalAlignment.Left
    wmHolderLayout.Parent = wmHolder

    wmHeader = Instance.new("Frame")
    wmHeader.Name = "Header"
    wmHeader.BackgroundColor3 = Color3.fromRGB(28, 28, 36)
    wmHeader.BackgroundTransparency = 0.15
    wmHeader.BorderSizePixel = 0
    wmHeader.Size = UDim2.new(0, 200, 0, watermarkSize + 8)
    wmHeader.LayoutOrder = 0

    local headerCorner = Instance.new("UICorner")
    headerCorner.CornerRadius = UDim.new(0, 4)
    headerCorner.Parent = wmHeader

    wmHeaderBar = Instance.new("Frame")
    wmHeaderBar.Name = "Bar"
    wmHeaderBar.BackgroundColor3 = espColor
    wmHeaderBar.BorderSizePixel = 0
    wmHeaderBar.Size = UDim2.new(0, 2, 1, 0)
    wmHeaderBar.ZIndex = 2
    wmHeaderBar.Parent = wmHeader

    local headerBarCorner = Instance.new("UICorner")
    headerBarCorner.CornerRadius = UDim.new(0, 4)
    headerBarCorner.Parent = wmHeaderBar

    wmHeaderLabel = Instance.new("TextLabel")
    wmHeaderLabel.Name = "HeaderText"
    wmHeaderLabel.BackgroundTransparency = 1
    wmHeaderLabel.Font = Enum.Font.Code
    wmHeaderLabel.TextSize = watermarkSize + 2
    wmHeaderLabel.TextColor3 = espColor
    wmHeaderLabel.Text = "Celestial | 0 FPS | " .. getCurrentTime()
    wmHeaderLabel.Size = UDim2.new(0, 0, 1, 0)
    wmHeaderLabel.AutomaticSize = Enum.AutomaticSize.X
    wmHeaderLabel.ZIndex = 3
    wmHeaderLabel.Parent = wmHeader

    wmHeaderLabel:GetPropertyChangedSignal("AbsoluteSize"):Connect(function()
        if not wmHeader then return end
        local w = wmHeaderLabel.AbsoluteSize.X + 20
        if w < 80 then w = 80 end
        wmHeader.Size = UDim2.new(0, w, 0, watermarkSize + 8)
        task.defer(recomputeWidth)
    end)

    wmHeader.Parent = wmHolder

    wmListFrame = Instance.new("Frame")
    wmListFrame.Name = "List"
    wmListFrame.BackgroundTransparency = 1
    wmListFrame.Size = UDim2.new(0, 200, 0, 0)
    wmListFrame.AutomaticSize = Enum.AutomaticSize.Y
    wmListFrame.LayoutOrder = 1
    wmListFrame.Parent = wmHolder

    wmListLayout = Instance.new("UIListLayout")
    wmListLayout.SortOrder = Enum.SortOrder.LayoutOrder
    wmListLayout.Padding = UDim.new(0, 2)
    wmListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Left
    wmListLayout.Parent = wmListFrame

    applyWatermarkLayout()
end

local function rebuildModuleRows()
    if not wmListFrame then return end

    for _, row in ipairs(wmModuleRows) do
        if row.frame then row.frame:Destroy() end
    end
    wmModuleRows = {}

    local modules = getActiveModules()
    for i, name in ipairs(modules) do
        local row = createModuleRow(wmListFrame, name, i)
        table.insert(wmModuleRows, row)
    end

    task.defer(recomputeWidth)
end

local function destroyWatermark()
    if wmGui then
        wmGui:Destroy()
        wmGui = nil
        wmHolder = nil
        wmHolderLayout = nil
        wmHeader = nil
        wmHeaderBar = nil
        wmHeaderLabel = nil
        wmListFrame = nil
        wmListLayout = nil
        wmModuleRows = {}
        wmMaxWidth = 0
    end
end

local function updateWatermark()
    if not watermarkEnabled then
        if wmGui then destroyWatermark() end
        return
    end

    if not wmGui then createWatermark() end

    if wmHeaderLabel then
        wmHeaderLabel.Text = "Celestial | " .. currentFps .. " FPS | " .. getCurrentTime()
    end

    rebuildModuleRows()
    applyWatermarkLayout()
end

-- ======================
-- TARGET ESP
-- ======================
local function destroyTargetMarker()
    if targetMarker then targetMarker:Destroy() targetMarker = nil end
    if targetScreenGui then targetScreenGui:Destroy() targetScreenGui = nil end
    currentTargetChar = nil
end

local function buildRings(parent)
    local ringConfigs = {
        { size = 90, thickness = 2, transparency = 0.0, speed = 1.0 },
        { size = 70, thickness = 2, transparency = 0.3, speed = 1.4 },
        { size = 50, thickness = 3, transparency = 0.5, speed = 1.8 },
    }

    for i, cfg in ipairs(ringConfigs) do
        local ring = Instance.new("Frame")
        ring.Name = "Ring" .. i
        ring.AnchorPoint = Vector2.new(0.5, 0.5)
        ring.Position = UDim2.new(0.5, 0, 0.5, 0)
        ring.Size = UDim2.new(0, cfg.size, 0, cfg.size)
        ring.BackgroundTransparency = 1
        ring.BorderSizePixel = 0
        ring.ZIndex = 10 - i

        local corner = Instance.new("UICorner")
        corner.CornerRadius = UDim.new(1, 0)
        corner.Parent = ring

        local stroke = Instance.new("UIStroke")
        stroke.Thickness = cfg.thickness
        stroke.Color = espColor
        stroke.Transparency = cfg.transparency
        stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
        stroke.Parent = ring

        ring:SetAttribute("BaseSize", cfg.size)
        ring:SetAttribute("BaseTransparency", cfg.transparency)
        ring:SetAttribute("PulseSpeed", cfg.speed)
        ring.Parent = parent
    end

    local dot = Instance.new("Frame")
    dot.Name = "CenterDot"
    dot.AnchorPoint = Vector2.new(0.5, 0.5)
    dot.Position = UDim2.new(0.5, 0, 0.5, 0)
    dot.Size = UDim2.new(0, 6, 0, 6)
    dot.BackgroundColor3 = espColor
    dot.BorderSizePixel = 0
    dot.ZIndex = 20
    local dotCorner = Instance.new("UICorner")
    dotCorner.CornerRadius = UDim.new(1, 0)
    dotCorner.Parent = dot
    dot.Parent = parent
end

local function createMarkerOverPlayer(char, offsetY)
    destroyTargetMarker()
    local head = char:FindFirstChild("Head") or char:FindFirstChild("Torso") or char:FindFirstChild("HumanoidRootPart")
    if not head then return end

    local billboard = Instance.new("BillboardGui")
    billboard.Name = "CelestialTargetESP"
    billboard.Size = UDim2.new(0, 120, 0, 120)
    billboard.StudsOffsetWorldSpace = Vector3.new(0, offsetY, 0)
    billboard.AlwaysOnTop = true
    billboard.LightInfluence = 0
    billboard.Adornee = head
    billboard.Parent = head

    buildRings(billboard)
    targetMarker = billboard
    currentTargetChar = char
end

local function createMarkerOnScreen()
    destroyTargetMarker()

    local sg = Instance.new("ScreenGui")
    sg.Name = "CelestialTargetScreen"
    sg.IgnoreGuiInset = true
    sg.ResetOnSpawn = false
    sg.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    sg.Parent = LocalPlayer:WaitForChild("PlayerGui")

    local holder = Instance.new("Frame")
    holder.Name = "Holder"
    holder.AnchorPoint = Vector2.new(0.5, 0.5)
    holder.Position = UDim2.new(0.5, 0, 0.5, 0)
    holder.Size = UDim2.new(0, 140, 0, 140)
    holder.BackgroundTransparency = 1
    holder.Parent = sg

    buildRings(holder)
    targetScreenGui = sg
    currentTargetChar = "SCREEN"
end

local function updateTargetMarker(target)
    if not targetESPEnabled or not target then
        if targetMarker or targetScreenGui then destroyTargetMarker() end
        return
    end

    local needRecreate = false
    if targetESPMode == "по центру экрана" then
        if targetMarker then needRecreate = true end
        if not targetScreenGui then needRecreate = true end
    else
        if targetScreenGui then needRecreate = true end
        if not targetMarker then needRecreate = true end
        if targetMarker and currentTargetChar ~= target then needRecreate = true end
    end

    if needRecreate then
        if targetESPMode == "над игроком" then
            createMarkerOverPlayer(target, 3.5)
        elseif targetESPMode == "по центру игрока" then
            createMarkerOverPlayer(target, 0)
        else
            createMarkerOnScreen()
        end
    end

    local container = targetMarker
    if targetScreenGui then container = targetScreenGui:FindFirstChild("Holder") end

    if container then
        local t = tick()
        for _, ring in ipairs(container:GetChildren()) do
            if ring:IsA("Frame") and ring.Name:sub(1, 4) == "Ring" then
                local baseSize = ring:GetAttribute("BaseSize")
                local baseTrans = ring:GetAttribute("BaseTransparency")
                local pulseSpeed = ring:GetAttribute("PulseSpeed")
                if baseSize then
                    local pulse = (math.sin(t * pulseSpeed * 3) + 1) / 2
                    local size = baseSize + pulse * 12
                    ring.Size = UDim2.new(0, size, 0, size)
                    local stroke = ring:FindFirstChildOfClass("UIStroke")
                    if stroke then stroke.Transparency = baseTrans + pulse * 0.3 end
                end
            end
        end
    end
end

-- ======================
-- ТЕМА
-- ======================
local function applyTheme(themeName)
    local theme = THEMES[themeName]
    if not theme then return end

    local customTheme = {
        TextColor = Color3.fromRGB(240, 240, 240),
        Background = theme.bg,
        Topbar = theme.bg,
        Shadow = Color3.fromRGB(10, 10, 10),
        NotificationBackground = theme.bg,
        NotificationActionsBackground = theme.button,
        TabBackground = theme.bg,
        TabStroke = theme.button,
        TabBackgroundSelected = theme.button,
        TabTextColor = Color3.fromRGB(240, 240, 240),
        SelectedTabTextColor = Color3.fromRGB(255, 255, 255),
        ElementBackground = Color3.fromRGB(30, 30, 30),
        ElementBackgroundHover = Color3.fromRGB(40, 40, 40),
        SecondaryElementBackground = Color3.fromRGB(25, 25, 25),
        ElementStroke = theme.button,
        SecondaryElementStroke = theme.button,
        SliderBackground = Color3.fromRGB(50, 50, 50),
        SliderProgress = theme.accent,
        SliderStroke = theme.accent,
        ToggleBackground = Color3.fromRGB(30, 30, 30),
        ToggleEnabled = theme.toggle,
        ToggleDisabled = Color3.fromRGB(100, 100, 100),
        ToggleEnabledStroke = theme.toggle,
        ToggleDisabledStroke = Color3.fromRGB(125, 125, 125),
        ToggleEnabledOuterStroke = theme.toggle,
        ToggleDisabledOuterStroke = Color3.fromRGB(65, 65, 65),
        DropdownSelected = Color3.fromRGB(40, 40, 40),
        DropdownUnselected = Color3.fromRGB(30, 30, 30),
        InputBackground = Color3.fromRGB(30, 30, 30),
        InputStroke = theme.button,
        PlaceholderColor = Color3.fromRGB(178, 178, 178),
    }

    if window then
        pcall(function()
            window:ModifyTheme(customTheme)
        end)
    end

    espColor = theme.esp
    applyESPColor()
end

-- ======================
-- KILL AURA
-- ======================
local function getTarget(originPos)
    if not killauraEnabled then return nil end

    local closest = nil
    local minDist = targetDist
    local otherChars = Workspace:FindFirstChild("OtherCharacters")
    if not otherChars then return nil end

    for _, char in ipairs(otherChars:GetChildren()) do
        local tRoot = char:FindFirstChild("Torso") or char:FindFirstChild("HumanoidRootPart")
        if tRoot then
            local dist = (tRoot.Position - originPos).Magnitude
            if dist <= minDist then
                minDist = dist
                closest = char
            end
        end
    end
    return closest
end

-- ======================
-- INPUT — Click TP
-- ======================
UserInputService.InputBegan:Connect(function(input, processed)
    if processed then return end

    if clickTpEnabled then
        local expectedKey = keyFromString(clickTpKey)
        if input.KeyCode == expectedKey then
            pcall(doClickTp)
        end
    end
end)

-- ======================
-- ГЛАВНЫЙ ЦИКЛ
-- ======================
RunService.RenderStepped:Connect(function(dt)
    fpsCounter = fpsCounter + 1
    fpsTime = fpsTime + dt
    if fpsTime >= 0.5 then
        currentFps = math.floor(fpsCounter / fpsTime + 0.5)
        fpsCounter = 0
        fpsTime = 0
        updateWatermark()
    end

    local otherChars = Workspace:FindFirstChild("OtherCharacters")
    if otherChars then
        if espEnabled then
            for _, char in ipairs(otherChars:GetChildren()) do
                if not char:FindFirstChild("CelestialESP") then
                    local hl = Instance.new("Highlight")
                    hl.Name = "CelestialESP"
                    hl.FillColor = espColor
                    hl.OutlineColor = espColor
                    hl.FillTransparency = espFill
                    hl.OutlineTransparency = 0
                    hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                    hl.Parent = char
                    espCache[char] = true
                end
            end
        else
            if next(espCache) then clearESP() end
        end
    end

    local myChar = getMyChar()

    if speedEnabled and myChar then
        local hitbox = myChar:FindFirstChild("PlayerHitbox")
        if hitbox then
            if not PlayerControls then
                local pm = LocalPlayer.PlayerScripts:FindFirstChild("PlayerModule")
                if pm then
                    PlayerControls = require(pm):GetControls()
                end
            end

            if PlayerControls then
                local moveVec = PlayerControls:GetMoveVector()
                if moveVec.Magnitude > 0 then
                    local look = Vector3.new(Camera.CFrame.LookVector.X, 0, Camera.CFrame.LookVector.Z)
                    local right = Vector3.new(Camera.CFrame.RightVector.X, 0, Camera.CFrame.RightVector.Z)

                    if look.Magnitude > 0 and right.Magnitude > 0 then
                        look = look.Unit
                        right = right.Unit
                        local moveDir = (look * -moveVec.Z + right * moveVec.X)
                        if moveDir.Magnitude > 0 then
                            moveDir = moveDir.Unit
                            hitbox.CFrame = hitbox.CFrame + (moveDir * speedBoost * dt)
                        end
                    end
                end
            end
        end
    end

    if flyEnabled and myChar then
        local hitbox = myChar:FindFirstChild("PlayerHitbox")
        if hitbox then
            local moveY = 0
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) or UserInputService:IsKeyDown(Enum.KeyCode.E) then
                moveY = moveY + (flySpeed * dt)
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.Q) or UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then
                moveY = moveY - (flySpeed * dt)
            end

            currentFlyY = currentFlyY + moveY

            local pos = hitbox.Position
            hitbox.CFrame = CFrame.new(pos.X, currentFlyY, pos.Z) * hitbox.CFrame.Rotation
            hitbox.AssemblyLinearVelocity = Vector3.zero
        end
    end

    -- Trails
    if trailsEnabled then
        if not trailObj or not trailObj.Parent then
            createTrail()
        end
    else
        if trailObj then destroyTrail() end
    end

    -- Chams
    if chamsEnabled then
        if tick() % 0.2 < dt then
            updateChams()
        end
    else
        if next(chamsHighlights) then clearChams() end
    end

    -- Blur
    if blurEnabled and not blurEffect then
        createBlur()
    elseif not blurEnabled and blurEffect then
        destroyBlur()
    end

    -- China Hat
    if chinaHatEnabled then
        if tick() % 0.3 < dt then
            updateChinaHat()
        end
    else
        if chinaHat then destroyChinaHat() end
    end

    -- FOV
    if fovEnabled then
        if Camera.FieldOfView ~= fovValue then
            Camera.FieldOfView = fovValue
        end
    end

    -- KillAura + Target marker
    local activeTarget = nil
    if killauraEnabled then
        local eyeLevel = myChar and myChar:FindFirstChild("PlayerEyeLevel")
        local originPos = eyeLevel and eyeLevel.Position or Camera.CFrame.Position
        activeTarget = getTarget(originPos)

        if activeTarget then
            local tRoot = activeTarget:FindFirstChild("Torso") or activeTarget:FindFirstChild("HumanoidRootPart")
            if tRoot and myChar then
                local headTorso = myChar:FindFirstChild("HeadTorso")
                local neck = headTorso and headTorso:FindFirstChild("Neck")

                if neck then
                    local lookDir = (tRoot.Position - originPos).Unit
                    local basePos = Vector3.new(0, 1.403, 0)
                    neck.C0 = CFrame.new(basePos, basePos + lookDir)
                end

                if tick() - lastHit >= hitCooldown then
                    local Remotes = ReplicatedStorage:FindFirstChild("Remotes")
                    if Remotes then
                        local AnimateHit = Remotes:FindFirstChild("AnimateHit")
                        local HitRequest = Remotes:FindFirstChild("HitRequest")

                        if AnimateHit and HitRequest then
                            local targetPlayerName = string.gsub(activeTarget.Name, "_FakeCharacter", "")
                            local targetPlayer = Players:FindFirstChild(targetPlayerName)
                            local dir = (tRoot.Position - originPos).Unit

                            AnimateHit:FireServer()
                            HitRequest:FireServer(originPos, dir, activeTarget, targetPlayer)
                            lastHit = tick()
                        end
                    end
                end
            end
        end
    end

    -- Target Strafe
    if targetStrafeEnabled and activeTarget then
        applyTargetStrafe(activeTarget, dt)
    elseif targetStrafeEnabled and not activeTarget then
        lastStrafeTarget = nil
    end

    updateTargetMarker(activeTarget)
end)

-- ======================
-- GUI
-- ======================
window = Rayfield:CreateWindow({
    name = "Celestial 1.12.2",
    subtitle = "1.8 Arena",
    sidebarLayout = true,
})

local combatTab = window:CreateTab({ name = "Бой", icon = 93364949241311 })

combatTab:CreateSection("Боевые модули")

local killAuraToggle = combatTab:CreateToggle({
    name = "Килл-аура",
    currentValue = killauraEnabled,
    callback = function(value)
        killauraEnabled = value
        updateWatermark()
    end,
})

combatTab:CreateSection("Цель")

local targetESPToggle = combatTab:CreateToggle({
    name = "Target ESP (кружки)",
    currentValue = targetESPEnabled,
    callback = function(value)
        targetESPEnabled = value
        if not value then destroyTargetMarker() end
        updateWatermark()
    end,
})

combatTab:CreateDropdown({
    name = "Позиция Target ESP",
    options = {"над игроком", "по центру игрока", "по центру экрана"},
    currentOption = "по центру игрока",
    callback = function(option)
        targetESPMode = option
        destroyTargetMarker()
        updateWatermark()
    end,
})

combatTab:CreateSection("Target Strafe — кружение вокруг цели")

local strafeToggle = combatTab:CreateToggle({
    name = "Target Strafe",
    currentValue = targetStrafeEnabled,
    callback = function(value)
        targetStrafeEnabled = value
        lastStrafeTarget = nil
        updateWatermark()
    end,
})

combatTab:CreateSlider({
    name = "Скорость кружения",
    range = {30, 720},
    increment = 10,
    suffix = " °/сек",
    currentValue = strafeSpeed,
    callback = function(value)
        strafeSpeed = value
    end,
})

combatTab:CreateSlider({
    name = "Радиус кружения",
    range = {3, 25},
    increment = 0.5,
    suffix = " studs",
    currentValue = strafeRadius,
    callback = function(value)
        strafeRadius = value
    end,
})

combatTab:CreateSection("Click Tp — телепорт к цели")

local clickTpToggle = combatTab:CreateToggle({
    name = "Click Tp",
    currentValue = clickTpEnabled,
    callback = function(value)
        clickTpEnabled = value
        updateWatermark()
    end,
})

combatTab:CreateInput({
    name = "Кнопка телепорта",
    currentValue = clickTpKey,
    placeholderText = "E",
    removeTextAfterFocusLost = false,
    callback = function(text)
        clickTpKey = text
    end,
})

local moveTab = window:CreateTab({ name = "Движение", icon = 93364949241311 })

moveTab:CreateSection("Модули движения")

local speedToggle = moveTab:CreateToggle({
    name = "Спидхак",
    currentValue = true,
    callback = function(value)
        speedEnabled = value
        updateWatermark()
    end,
})

moveTab:CreateSection("Полёт")

local flyToggle = moveTab:CreateToggle({
    name = "Полёт (Fly)",
    currentValue = flyEnabled,
    callback = function(value)
        flyEnabled = value
        if value then
            local myChar = getMyChar()
            local hitbox = myChar and myChar:FindFirstChild("PlayerHitbox")
            if hitbox then currentFlyY = hitbox.Position.Y end
        end
        updateWatermark()
    end,
})

moveTab:CreateSlider({
    name = "Скорость полёта",
    range = {10, 150},
    increment = 5,
    suffix = " studs",
    currentValue = flySpeed,
    callback = function(value) flySpeed = value end,
})

local effectsTab = window:CreateTab({ name = "Эффекты", icon = 93364949241311 })

effectsTab:CreateSection("Trails — след из света")

local trailsToggle = effectsTab:CreateToggle({
    name = "Trails",
    currentValue = trailsEnabled,
    callback = function(value)
        trailsEnabled = value
        if value then createTrail() else destroyTrail() end
        updateWatermark()
    end,
})

effectsTab:CreateColorPicker({
    name = "Цвет Trails",
    color = trailColor,
    callback = function(color)
        trailColor = color
        updateTrailColor()
    end,
})

effectsTab:CreateSection("Chams — перекраска предметов")

local chamsToggle = effectsTab:CreateToggle({
    name = "Chams (предметы в руках)",
    currentValue = chamsEnabled,
    callback = function(value)
        chamsEnabled = value
        if not value then clearChams() end
        updateWatermark()
    end,
})

effectsTab:CreateColorPicker({
    name = "Цвет Chams",
    color = chamsColor,
    callback = function(color)
        chamsColor = color
        updateChamsColor()
    end,
})

effectsTab:CreateSection("Blur — блюр экрана")

local blurToggle = effectsTab:CreateToggle({
    name = "Blur",
    currentValue = blurEnabled,
    callback = function(value)
        blurEnabled = value
        updateBlur()
        updateWatermark()
    end,
})

effectsTab:CreateSlider({
    name = "Сила блюра",
    range = {0, 40},
    increment = 1,
    suffix = "",
    currentValue = blurSize,
    callback = function(value)
        blurSize = value
        if blurEffect then blurEffect.Size = blurSize end
    end,
})

effectsTab:CreateSection("China Hat — китайская шляпа (только на себе)")

local chinaHatToggle = effectsTab:CreateToggle({
    name = "China Hat",
    currentValue = chinaHatEnabled,
    callback = function(value)
        chinaHatEnabled = value
        if value then
            createChinaHat()
        else
            destroyChinaHat()
        end
        updateWatermark()
    end,
})

effectsTab:CreateColorPicker({
    name = "Цвет China Hat",
    color = chinaHatColor,
    callback = function(color)
        chinaHatColor = color
        updateChinaHatColor()
    end,
})

effectsTab:CreateSection("Fullbright — убрать темноту")

local fullbrightToggle = effectsTab:CreateToggle({
    name = "Fullbright",
    currentValue = fullbrightEnabled,
    callback = function(value)
        fullbrightEnabled = value
        updateFullbright()
        updateWatermark()
    end,
})

local visualTab = window:CreateTab({ name = "Визуал", icon = 93364949241311 })

visualTab:CreateSection("Подсветка игроков")

local espToggle = visualTab:CreateToggle({
    name = "ESP игроков",
    currentValue = true,
    callback = function(value)
        espEnabled = value
        if not value then clearESP() end
        updateWatermark()
    end,
})

visualTab:CreateColorPicker({
    name = "Цвет ESP",
    color = espColor,
    callback = function(color)
        espColor = color
        applyESPColor()
    end,
})

visualTab:CreateSlider({
    name = "Прозрачность ESP",
    range = {0, 1},
    increment = 0.05,
    suffix = "",
    currentValue = espFill,
    callback = function(value)
        espFill = value
        applyESPColor()
    end,
})

visualTab:CreateSection("FOV Changer")

local fovToggle = visualTab:CreateToggle({
    name = "FOV Changer",
    currentValue = fovEnabled,
    callback = function(value)
        fovEnabled = value
        updateFOV()
        updateWatermark()
    end,
})

visualTab:CreateSlider({
    name = "FOV",
    range = {70, 130},
    increment = 1,
    suffix = "°",
    currentValue = fovValue,
    callback = function(value)
        fovValue = value
        if fovEnabled then Camera.FieldOfView = fovValue end
    end,
})

visualTab:CreateSection("Интерфейс")

local watermarkToggle = visualTab:CreateToggle({
    name = "Watermark",
    currentValue = watermarkEnabled,
    callback = function(value)
        watermarkEnabled = value
        if not value then destroyWatermark() end
        updateWatermark()
    end,
})

visualTab:CreateDropdown({
    name = "Сторона Watermark",
    options = {"слева", "справа"},
    currentOption = "слева",
    callback = function(option)
        watermarkSide = option
        updateWatermark()
    end,
})

visualTab:CreateSlider({
    name = "Размер Watermark",
    range = {9, 22},
    increment = 1,
    suffix = " px",
    currentValue = watermarkSize,
    callback = function(value)
        watermarkSize = value
        updateWatermark()
    end,
})

local settingsTab = window:CreateTab({ name = "Настройки", icon = 93364949241311 })

settingsTab:CreateSection("Темы оформления")

settingsTab:CreateDropdown({
    name = "Выбор темы",
    options = {
        "Classic (красная)",
        "Celestial (фиолетовая)",
        "Ocean (синяя)",
        "Matrix (зелёная)",
        "Mono (серая)",
    },
    currentOption = "Classic (красная)",
    callback = function(option)
        applyTheme(option)
    end,
})

settingsTab:CreateSection("Информация")

settingsTab:CreateParagraph({
    title = "Celestial 1.12.2",
    content = "Килл-аура | Спидхак | ESP | Полёт | Target ESP | Watermark | Trails | Chams | Blur | Target Strafe | China Hat | FOV | Fullbright | Click Tp\n\nУправление только через GUI.\n\nПолёт: Space/E — вверх, Q/LeftShift — вниз\nStrafe: LeftShift — смена направления",
})

settingsTab:CreateSection("Конфигурация")

settingsTab:CreateButton({
    name = "Сбросить настройки",
    callback = function()
        killauraEnabled = true
        speedEnabled = true
        espEnabled = true
        flyEnabled = false
        targetESPEnabled = true
        watermarkEnabled = true
        trailsEnabled = false
        chamsEnabled = false
        blurEnabled = false
        targetStrafeEnabled = false
        chinaHatEnabled = false
        fovEnabled = false
        fullbrightEnabled = false
        clickTpEnabled = false
        watermarkSide = "слева"
        watermarkSize = 13
        espColor = Color3.fromRGB(200, 100, 255)
        chamsColor = Color3.fromRGB(200, 100, 255)
        trailColor = Color3.fromRGB(200, 100, 255)
        chinaHatColor = Color3.fromRGB(255, 50, 50)
        blurSize = 10
        strafeRadius = 6.5
        strafeSpeed = 180
        fovValue = 100
        espFill = 0.5
        killAuraToggle:Set(true)
        speedToggle:Set(true)
        espToggle:Set(true)
        flyToggle:Set(false)
        targetESPToggle:Set(true)
        watermarkToggle:Set(true)
        trailsToggle:Set(false)
        chamsToggle:Set(false)
        blurToggle:Set(false)
        strafeToggle:Set(false)
        chinaHatToggle:Set(false)
        fovToggle:Set(false)
        fullbrightToggle:Set(false)
        clickTpToggle:Set(false)
        applyESPColor()
        updateChamsColor()
        updateTrailColor()
        updateBlur()
        updateChinaHatColor()
        updateFOV()
        updateFullbright()
        updateWatermark()
        Rayfield:Notify({ title = "Celestial", content = "Настройки сброшены" })
    end,
})

-- ======================
-- СТАРТ
-- ======================
updateWatermark()

Rayfield:Notify({ title = "Celestial 1.12.2", content = "Чит загружен" })

print("[Celestial] 1.12.2 loaded")
