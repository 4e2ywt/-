---deobfuscator by ً‌‘¨ً‌‘©ً‌‘¶ً‌‘¶ً‌‘«/ً‌‘¨ً‌’€ً‌‘´ً‌‘¨ً‌‘µ

-- ============================================================
-- ط§ظ„ظ…طھط؛ظٹط±ط§طھ ط§ظ„ط¹ط§ظ…ط© (Forward Declarations)
-- ============================================================
local whitelist, localPlayer
local checkCondition
local virtualUserService
local playersService, playerGui, localPlayerRef
local coreGuiTarget

-- ============================================================
-- 1. ط¯ط§ظ„ط© ط§ظ„طھط­ظ‚ظ‚ ظ…ظ† ط§ظ„ظ‚ط§ط¦ظ…ط© ط§ظ„ط¨ظٹط¶ط§ط،
-- ============================================================
local function checkCondition()
    if whitelist[localPlayer.Name] 
       or whitelist[localPlayer.DisplayName] 
       or whitelist[localPlayer.UserId] then
        print("èٹ’èپ¹èپں [LEO HUB] è„µèپںè„´هچ¤è„´é¢…è„´ç¯“è„´وگ‚è„µèپ¥ " .. localPlayer.Name)
        return true
    else
        return false
    end
end

-- ============================================================
-- 2. ط®ط¯ظ…ط© VirtualUser
-- ============================================================
local function getVirtualUserService()
    virtualUserService = game:GetService("VirtualUser")
end

-- ============================================================
-- 3. ط¯ط§ظ„ط© ط§ظ†طھط¸ط§ط± PlayerGui (ط­ط±ظپظٹط© ط¨ط§ظ„ظƒط§ظ…ظ„)
-- ============================================================
local function waitForChild()
    local function getService()
        return game:GetService("CoreGui")
    end
    local a
    local function createFolder()
        local folder = Instance.new("Folder")
        folder.Parent = a
        folder:Destroy()
    end
    a = getService
    local pack = table.pack(pcall(getService))
    local b = table.unpack(pack, 1, pack.n)
    if b then
        a = table.unpack(pack, 2, pack.n)
        b = a
    end
    if b then
        pcall(createFolder)
        a = table.unpack(pack, 2, pack.n)
        return a
    end
    b = localPlayerRef:WaitForChild("PlayerGui")
    a = table.unpack(pack, 2, pack.n)
    return b
end

-- ============================================================
-- 4. ط¯ط§ظ„ط© ط¥ط¶ط§ظپط© UIScale
-- ============================================================
local uiScaleEnabled, uiScaleAmount

local function addUIScale(parent)
    local uIScale
    if uiScaleEnabled then
        uIScale = Instance.new("UIScale")
        uIScale.Scale = uiScaleAmount
        uIScale.Parent = parent
    end
end

-- ============================================================
-- 5. ط¬ط¯ظˆظ„ طھطھط¨ط¹ ط§ظ„ظ„ط§ط¹ط¨ظٹظ†
-- ============================================================
local playerStats

local function onPlayerJoined(player)
    if not playerStats[player.Name] then
        local stats = {
            joins = 1,
            leaves = 0,
            isOnline = true,
            userId = player.UserId,
        }
        playerStats[player.Name] = stats
    else
        playerStats[player.Name].joins = playerStats[player.Name].joins + 1
        playerStats[player.Name].isOnline = true
    end
end

local function onPlayerLeft(player)
    if playerStats[player.Name] then
        playerStats[player.Name].leaves = playerStats[player.Name].leaves + 1
        playerStats[player.Name].isOnline = false
    end
end

-- ============================================================
-- 6. ط¯ظˆط§ظ„ ط¥ط®ظپط§ط، ط§ظ„ظ…ط¤ط«ط±ط§طھ
-- ============================================================
local hideEffectsEnabled
local hideEffectsFunction

local function tryHideEffect(instance)
    if not hideEffectsEnabled or not instance then
        return
    end
    instance:IsA("Explosion")
    instance:IsA("ParticleEmitter")
    instance:IsA("Sparkles")
    instance:IsA("Fire")
    instance:IsA("Smoke")
    instance:IsA("Trail")
    instance:IsA("Beam")
    instance:IsA("Highlight")
    instance:IsA("SelectionBox")
    instance:IsA("Decal")
    instance:IsA("Texture")
    instance:IsA("Sound")
    instance:IsA("BasePart")
    return
end

-- ============================================================
-- 7. طھظپط¹ظٹظ„ ط¥ط®ظپط§ط، ط§ظ„ظ…ط¤ط«ط±ط§طھ (ط­ط±ظپظٹ)
-- ============================================================
local lightingService
local hideEffectsConnection

local function connectEffectHider()
    local function applyLighting()
        lightingService.GlobalShadows = false
        lightingService.FogEnd = 9000000000
        lightingService.Brightness = 1
    end
    local function hideAllExisting()
        local descendants = workspace:GetDescendants()
        table.pack(next(descendants, nil))
        local b = next(descendants)
        local c
        if not (b == nil) then
            c = b
            while true do
                hideEffectsFunction(descendants)
                table.pack(next(descendants, c))
                b = next(descendants)
                if b ~= nil then
                    c = b
                    continue
                end
                break
            end
        end
        return next, descendants, b, next(descendants)
    end
    local function onDescendantAdded(instance)
        if hideEffectsEnabled then
            hideEffectsFunction(instance)
            return instance
        else
            return
        end
    end
    hideEffectsEnabled = not hideEffectsEnabled
    if hideEffectsEnabled then
        pcall(applyLighting)
        pcall(hideAllExisting)
        hideEffectsConnection = workspace.DescendantAdded:Connect(onDescendantAdded)
    else
        return
    end
end

-- ============================================================
-- 8. ط¯ظˆط§ظ„ ظپط­طµ ط§ظ„ط£ط­ط¬ط§ظ…
-- ============================================================
local function isLargePart(part)
    local size
    if not part:IsA("BasePart") then
        return false
    else
        size = part.Size
        return 50 <= size.X and 50 <= size.Z
    end
end

-- ============================================================
-- 9. ظپط­طµ ط§ظ„ط´ط®طµظٹط©
-- ============================================================
local playersServiceRef

local function isPlayerCharacter(instance)
    local current = instance
    while current do
        if not (current:IsA("Model") and playersServiceRef:GetPlayerFromCharacter(current)) then
            current = current.Parent
            continue
        end
        return true
    end
    return false
end

-- ============================================================
-- 10. ط¥ط®ظپط§ط، ط¥ط¶ط§ظپظٹ (Anti-Lag)
-- ============================================================
local antiLagActive
local hideAdditionalFunction

local function tryHideAdditional(instance)
    local function updateMaterial()
        instance.Material = Enum.Material.SmoothPlastic
        instance.Reflectance = 0
        instance.CastShadow = false
    end
    local function hideDecal()
        instance.LocalTransparencyModifier = 1
        instance.Transparency = 1
        instance.CanCollide = false
        instance.CastShadow = false
    end
    local function hideTexture()
        instance.Transparency = 1
    end
    local function disableEffect()
        instance.Enabled = false
    end
    local function muteSound()
        instance.Volume = 0
    end
    if not (not antiLagActive or not instance) then
        instance:IsA("BasePart")
        instance:IsA("Decal")
        instance:IsA("Texture")
        instance:IsA("ParticleEmitter")
        instance:IsA("Trail")
        instance:IsA("Beam")
        instance:IsA("Fire")
        instance:IsA("Smoke")
        instance:IsA("Sparkles")
        instance:IsA("Highlight")
        instance:IsA("Sound")
        return
    end
end

-- ============================================================
-- 11. طھظپط¹ظٹظ„ Anti-Lag (ط­ط±ظپظٹ)
-- ============================================================
local antiLagConnection
local antiLagEnabled2

local function connectAntiLag()
    local function applyLighting()
        lightingService.GlobalShadows = false
        lightingService.FogEnd = 1000000
        lightingService.Brightness = 0.5
        lightingService.EnvironmentDiffuseScale = 0
        lightingService.EnvironmentSpecularScale = 0
        local children = lightingService:GetChildren()
        if not (next(children) == nil) then
            while true do
                if next(children) == nil then
                    break
                end
            end
        end
    end
    local function applyTerrain()
        workspace.Terrain.WaterWaveSize = 0
        workspace.Terrain.WaterWaveSpeed = 0
        workspace.Terrain.WaterReflectance = 0
        workspace.Terrain.WaterTransparency = 0
    end
    local function backgroundHide()
        local descendants = workspace:GetDescendants()
        local a = next
        local b = next(descendants)
        local c
        if not (b == nil) then
            while true do
                if antiLagEnabled2 then
                    b = next(descendants)
                    if b == nil then
                        c = b
                    else
                        continue
                    end
                else
                    b = next(descendants)
                    if b == nil then
                        break
                    else
                        continue
                    end
                end
                return a, descendants, c, nil
            end
        end
        return next, descendants, b, nil
    end
    local function onDescendantAdded(instance)
        local function backgroundHide2()
            hideAdditionalFunction(instance)
        end
        if antiLagEnabled2 then
            task.defer(backgroundHide2)
            return instance
        else
            return
        end
    end
    if not antiLagEnabled2 then
        antiLagEnabled2 = true
        pcall(applyLighting)
        pcall(applyTerrain)
        task.spawn(backgroundHide)
        antiLagConnection = workspace.DescendantAdded:Connect(onDescendantAdded)
        return
    end
end

local function disconnectAntiLag()
    antiLagEnabled2 = false
    if antiLagConnection then
        antiLagConnection:Disconnect()
        antiLagConnection = nil
    end
end

-- ============================================================
-- 12. ط¯ظˆط§ظ„ ط¥ط®ظپط§ط، ط§ظ„ظ„ط§ط¹ط¨ظٹظ†
-- ============================================================
local hiddenPlayersTable
local checkPlayerHidden

local function checkPlayerHidden(player)
    if not player then
        return false
    elseif player == localPlayerRef then
        return hiddenPlayersTable[player.Name] == true
    else
        return hiddenPlayersTable[player.Name] == true
    end
end

local function hideCharacter(character)
    local b
    local function updateCFrame()
        b.CFrame = CFrame.new(0, -50000, 0)
    end
    local c
    local function hideTransparency()
        c.LocalTransparencyModifier = 1
        c.Transparency = 1
    end
    local function hideTexture()
        c.Transparency = 1
    end
    local function destroyDecal()
        c:Destroy()
    end
    local function disableEffect()
        c.Enabled = false
    end
    b = not character or not character.Parent
    local d, e, f, g, h
    if not b then
        b = character:FindFirstChild("UpperTorso")
        character:GetDescendants()
        d = ipairs(e)
        h = table.pack(d(e, 0))
        g = table.unpack(h, 1, h.n)
        if not (g == nil) then
            f = g
            while true do
                c = table.unpack(h, 2, h.n)
                if c:IsA("BasePart") then
                    pcall(hideTransparency)
                    h = table.pack(d(e, f))
                    g = table.unpack(h, 1, h.n)
                    if g == nil then
                        f = g
                    else
                        f = g
                        continue
                    end
                else
                    c = table.unpack(h, 2, h.n)
                    c:IsA("Decal")
                    c = table.unpack(h, 2, h.n)
                    c:IsA("Texture")
                    c = table.unpack(h, 2, h.n)
                    c:IsA("Highlight")
                    c = table.unpack(h, 2, h.n)
                    c:IsA("SelectionBox")
                    c = table.unpack(h, 2, h.n)
                    c:IsA("BoxHandleAdornment")
                    c = table.unpack(h, 2, h.n)
                    c:IsA("BillboardGui")
                    c = table.unpack(h, 2, h.n)
                    c:IsA("SurfaceGui")
                    c = table.unpack(h, 2, h.n)
                    h = table.pack(d(e, f))
                    g = table.unpack(h, 1, h.n)
                    if g == nil then
                        break
                    else
                        f = g
                        continue
                    end
                end
                return character, b, d, e, f, table.unpack(h, 1, h.n)
            end
        end
        return character, b, d, e, g, table.unpack(h, 1, h.n)
    end
end

local function showCharacter(character)
    local b
    local function restoreTransparency()
        b.LocalTransparencyModifier = 0
        b.Transparency = 0
    end
    local function restoreTexture()
        b.Transparency = 0
    end
    local function destroyDecal()
        b:Destroy()
    end
    local function enableEffect()
        b.Enabled = true
    end
    local c, d, e, f, g
    if not (not character or not character.Parent) then
        character:GetDescendants()
        c = ipairs(d)
        g = table.pack(c(d, 0))
        f = table.unpack(g, 1, g.n)
        if not (f == nil) then
            e = f
            while true do
                b = table.unpack(g, 2, g.n)
                if b:IsA("BasePart") then
                    pcall(restoreTransparency)
                    g = table.pack(c(d, e))
                    f = table.unpack(g, 1, g.n)
                    if f == nil then
                        e = f
                    else
                        e = f
                        continue
                    end
                else
                    b = table.unpack(g, 2, g.n)
                    b:IsA("Decal")
                    b = table.unpack(g, 2, g.n)
                    b:IsA("Texture")
                    b = table.unpack(g, 2, g.n)
                    b:IsA("Highlight")
                    b = table.unpack(g, 2, g.n)
                    b:IsA("SelectionBox")
                    b = table.unpack(g, 2, g.n)
                    b:IsA("BoxHandleAdornment")
                    b = table.unpack(g, 2, g.n)
                    b:IsA("BillboardGui")
                    b = table.unpack(g, 2, g.n)
                    b:IsA("SurfaceGui")
                    b = table.unpack(g, 2, g.n)
                    g = table.pack(c(d, e))
                    f = table.unpack(g, 1, g.n)
                    if f == nil then
                        break
                    else
                        e = f
                        continue
                    end
                end
                return character, c, d, e, table.unpack(g, 1, g.n)
            end
        end
        return character, c, d, f, table.unpack(g, 1, g.n)
    end
end

-- ============================================================
-- 13. ط±ط¨ط· ط£ط­ط¯ط§ط« ط§ظ„ظ„ط§ط¹ط¨ظٹظ†
-- ============================================================
local checkPlayerFunc, showPlayerFunc
local hidePlayerFunc, transformPlayerFunc

local function transformPlayer(a)
    if not (not a or not a.Character) then
        checkPlayerFunc(a)
        showPlayerFunc(a.Character)
        return a
    end
end

local connectCharacterAdded

local function connectEvents5(a)
    local function onCharacterAdded(b)
        task.wait(0.1)
        if checkPlayerFunc(a) then
            hidePlayerFunc(b)
        end
        return b
    end
    transformPlayerFunc(a)
    a.CharacterAdded:Connect(onCharacterAdded)
    return a
end

-- ============================================================
-- 14. ظ…ط±ط§ظ‚ط¨ط© ط¥ط¶ط§ظپط§طھ Workspace
-- ============================================================
local function onDescendantAdded3(instance)
    local function destroyInstance()
        local function destroyInner()
            instance:Destroy()
        end
        local function destroyOuter()
            instance:Destroy()
        end
        local parent = instance.Parent
        local getPlayerFromCharacter, getPlayerFromCharacter2
        if parent then
            getPlayerFromCharacter = playersServiceRef:GetPlayerFromCharacter(parent)
            if getPlayerFromCharacter and checkPlayerFunc(getPlayerFromCharacter) then
                pcall(destroyInner)
                parent:IsA("BasePart")
                getPlayerFromCharacter2 = playersServiceRef:GetPlayerFromCharacter(parent.Parent)
                if getPlayerFromCharacter2 and checkPlayerFunc(getPlayerFromCharacter2) then
                    pcall(destroyOuter)
                    return parent, getPlayerFromCharacter, getPlayerFromCharacter2
                end
                return parent, getPlayerFromCharacter
            else
                parent:IsA("BasePart")
                return
            end
        end
    end
    if not (not hideEffectsEnabled and next(hiddenPlayersTable) == nil) then
        instance:IsA("Highlight")
        instance:IsA("SelectionBox")
        instance:IsA("BoxHandleAdornment")
        return
    end
end

-- ============================================================
-- 15. ط­ظ„ظ‚ط© Heartbeat ظ„ط¥ط®ظپط§ط، ط§ظ„ظ„ط§ط¹ط¨ظٹظ† (ط­ط±ظپظٹط©)
-- ============================================================
local function onHeartbeat4()
    local a
    local function updateCFrame()
        a.CFrame = CFrame.new(0, -50000, 0)
    end
    local b
    local function destroyInstance()
        b:Destroy()
    end
    local function hideTransparency()
        b.LocalTransparencyModifier = 1
    end
    local c, d, e, f, character, g, h, i, j, l, m
    if not (not hideEffectsEnabled and next(hiddenPlayersTable) == nil) then
        playersServiceRef:GetPlayers()
        c = ipairs(d)
        m = table.pack(c(d, 0))
        f = table.unpack(m, 1, m.n)
        if not (f == nil) then
            e = f
            while true do
                a = table.unpack(m, 2, m.n)
                if checkPlayerFunc(a) and m[2].Character then
                    character = m[2].Character
                    a = character:FindFirstChild("HumanoidRootPart") 
                     or character:FindFirstChild("Torso") 
                     or character:FindFirstChild("UpperTorso")
                    if a then
                        pcall(updateCFrame)
                    end
                    h = character:GetChildren()
                    g = ipairs(h)
                    h = select(2, ipairs(h))
                    m = table.pack(g(h, 0))
                    j = table.unpack(m, 1, m.n)
                    if not (j == nil) then
                        i = j
                        while true do
                            b = table.unpack(m, 2, m.n)
                            l = b:IsA("Highlight")
                            if not l then
                                b = table.unpack(m, 2, m.n)
                                l = b:IsA("SelectionBox")
                            end
                            if not l then
                                b = table.unpack(m, 2, m.n)
                                l = b:IsA("BoxHandleAdornment")
                            end
                            if l then
                                pcall(destroyInstance)
                                m = table.pack(g(h, i))
                                j = table.unpack(m, 1, m.n)
                                if not (j == nil) then
                                    i = j
                                    continue
                                end
                            else
                                b = table.unpack(m, 2, m.n)
         
