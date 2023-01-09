Debug = true
function Debug(...)
    if Debug then
        print(...)
    end
end

-- Covering problematic meshparts with parts so navmesh works better

local l__Events2__8 = game.ReplicatedStorage:WaitForChild("Events2");
local l__StaminaChange__36 = l__Events2__8:WaitForChild("StaminaChange");

local StaminaPercent = 0
l__StaminaChange__36.Event:Connect(function(p3,p4)
    StaminaPercent = math.floor((p3/p4) * 100)
end)

-- // __newindex hook
local __newindex
__newindex = hookmetamethod(game, "__newindex", function(t, k, v)

    if (not checkcaller() and t:IsA("Humanoid") and (k == "WalkSpeed" or k == "JumpPower")) then
        return
    end
    
    -- //
    return __newindex(t, k, v)
end)

for i,v in pairs(getgc(true)) do
    if typeof(v) == 'table' and typeof(rawget(v, 'A')) == "function" then
        v.A = function()

        end
    end
    if typeof(v) == 'table' and typeof(rawget(v, 'B')) == "function" then
        v.B = function()

        end
    end
    if typeof(v) == 'table' and rawget(v, 'GP') then
        v.GP = function()

        end
    end
    if typeof(v) == 'table' and rawget(v, 'EN') then
        v.EN = function()

        end
    end
end

pcall(function()
    game:GetService("ReplicatedStorage").Events.__DFfDD:Destroy()
end)

for i,v in pairs(workspace:GetDescendants()) do
    if v:IsA("MeshPart") and (v.MeshId == "rbxassetid://6507305991" or v.MeshId == "rbxassetid://7555262504" or v.MeshId == "rbxassetid://7603957648" or v.MeshId == "rbxassetid://6507305956") then
        if not v:IsDescendantOf(workspace.Map.Doors) then
            local CLP = Instance.new("Part")
            CLP.Anchored = true
            CLP.Position = v.Position
            CLP.Orientation = v.Orientation
            CLP.Size = v.Size
            CLP.Parent = workspace
            CLP.Transparency = 0.5
            CLP.Color = Color3.new(1, 0.568627, 0)
        end
    end
    if v.Name == "BarbedWire" then
        v:Destroy()
    end
    if v:IsA("Part") and v.Size.Y == 13 and v.Color == Color3.fromRGB(17, 17, 17) and v.Material == Enum.Material.Metal then
        v:Destroy()
    end
end

-- Part to stop from trying to walk up railings on tower (Breaks the pathing for some reason)

-- local BlockingPart = Instance.new("Part")
-- BlockingPart.Position = Vector3.new(-4460.05, 64.9998, -800.67)
-- BlockingPart.Size = Vector3.new(1, 123.1, 23.7)
-- BlockingPart.Orientation = Vector3.new(0, 0, 0)
-- BlockingPart.Parent = workspace
-- BlockingPart.Color = Color3.new(1, 0.568627, 0)
-- BlockingPart.Transparency = 0.5
-- BlockingPart.Anchored = true

local ScreenGui = Instance.new("ScreenGui"); local StatusLabel = Instance.new("TextLabel")

ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui"); ScreenGui.ResetOnSpawn = false; ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling; ScreenGui.DisplayOrder = 1000; StatusLabel.Parent = ScreenGui; StatusLabel.BackgroundColor3 = Color3.fromRGB(255, 255, 255);
StatusLabel.BackgroundTransparency = 1.000; StatusLabel.Position = UDim2.new(0.5, -190, 0.553361773, -25); StatusLabel.Size = UDim2.new(0, 380, 0, 29); StatusLabel.Font = Enum.Font.Code; StatusLabel.Text = "Loading autofarm..."; StatusLabel.TextColor3 = Color3.fromRGB(252, 255, 69)
StatusLabel.TextScaled = false; StatusLabel.TextSize = 20; StatusLabel.TextStrokeTransparency = 0.000; StatusLabel.TextWrapped = false

--//

local LocalizationService = game:GetService("LocalizationService")
local LocalPlayer = game:GetService("Players").LocalPlayer
local Players = game:GetService("Players")

local function Status(Status)
    StatusLabel.Text = Status
end

local Safes = workspace.Map.BredMakurz
local SafeTypes = {"MediumSafe","SmallSafe","Register"}

--\\

local LockSprint = false
local PathfindingService = game:GetService("PathfindingService")
local Character = LocalPlayer.Character
local PlayerMod = require(LocalPlayer.PlayerScripts:WaitForChild("PlayerModule"))
local Controls = PlayerMod:GetControls()
local CurrentlyPathing = false
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local TweenI = TweenInfo.new(1,Enum.EasingStyle.Quint,Enum.EasingDirection.Out)
local VisualFolder = Instance.new("Folder",workspace)
local CurrentWaypoint = nil
local Pass = false
VisualFolder.Name = "PathVisuals"

CurrentPath = nil

local DoorIgnoreFolder = Instance.new("Folder")
DoorIgnoreFolder.Parent = workspace
DoorIgnoreFolder.Name = "DoorIgnoreFolder"

for i,v in pairs(workspace.Map.Doors:GetChildren()) do
    if v:FindFirstChild("DFrame") then
        local DoorBase = v:FindFirstChild("DFrame")

        local DoorIgnorePart = Instance.new("Part")
        DoorIgnorePart.Transparency = 0.9
        DoorIgnorePart.Anchored = true
        DoorIgnorePart.Color = Color3.new(0.2, 0.772549, 0.058823)
        DoorIgnorePart.CanCollide = false
        DoorIgnorePart.Position = DoorBase.Position
        DoorIgnorePart.Orientation = DoorBase.Orientation
        DoorIgnorePart.Size = DoorBase.Size + Vector3.new(0.5,0.5,0.5)

        local DoorIgnoreLink = Instance.new("PathfindingModifier",DoorIgnorePart)
        DoorIgnoreLink.PassThrough = true

        DoorIgnorePart.Parent = DoorIgnoreFolder
    end
end

--/

local function TTP(Position : CFrame)

    if not Pass then
        if typeof(Position) == "Instance" then
            Position = Position.CFrame
        end
    
        if typeof(Position) == "Vector3" then
            Position = CFrame.new(Position)
        end
    
        if typeof(Position) ~= "CFrame" then
            warn("[!] Invalid Argument Passed to TTP()")
        else
            local OP = LocalPlayer.Character.HumanoidRootPart.Position
            local TTW = (OP - Position.Position).Magnitude / 5
        
            local Tween = TweenService:Create(LocalPlayer.Character.HumanoidRootPart,TweenInfo.new(TTW),{CFrame = Position})
            Tween:Play()
            Tween.Completed:Wait()
        end
    end
    
    
end;

local function UpdateVisualPoint(Point,Remove,Color)
	task.spawn(function()
		if Remove == true then
			TweenService:Create(Point,TweenI,{Color3 = Color3.new(0.454902, 0.454902, 0.454902)}):Play()
			TweenService:Create(Point,TweenI,{Transparency = 1}):Play()
			wait(1)
			Point.Parent:Destroy()
		else
			TweenService:Create(Point,TweenI,{Color3 = Color}):Play()
		end end)
end

local WalkTrack = game.Players.LocalPlayer.Character.Humanoid:LoadAnimation(LocalPlayer.Character.Animate.walk1.WalkAnim1)
local RunTrack = game.Players.LocalPlayer.Character.Humanoid:LoadAnimation(LocalPlayer.Character.Animate.run1.RunAnim1)
WalkTrack.Looped = true
RunTrack.Looped = true

local function Walk(bool)
    if bool == true and LockSprint == false and WalkTrack.IsPlaying == false then
        WalkTrack:Play(0.5,2,0.8)
        RunTrack:Stop(0.5)
    elseif bool == false then
        WalkTrack:Stop(0.5)
    end
end

local function Run(bool)
    if bool == true and LockSprint == true and RunTrack.IsPlaying == false then
        if WalkTrack.IsPlaying == true then
            Walk(false)
        end
        RunTrack:Play(0.5,3,1.2)
    elseif bool == false then
        RunTrack:Stop(0.5)
    end
end

LockSprint = true

LocalPlayer.Character.Humanoid.HealthChanged:Connect(function(health)
    if health < 60 then
        LockSprint = true
    end
end)

task.spawn(function()
    while true do
        LockSprint = true
        LocalPlayer.Character.Humanoid.WalkSpeed = 25
        wait(17)
        LocalPlayer.Character.Humanoid.WalkSpeed = 10
        LockSprint = false
        wait(10)
    end
end)

local function FakeAnimationHandler()
    RunService.Heartbeat:Connect(function()
        local Velocity = LocalPlayer.Character.Torso.Velocity.Magnitude
        if Velocity > 0.1 and CurrentlyPathing then
            if LockSprint == true then
                Run(true)
            else
                Walk(true)
            end
        end
    end)
end

local function CreateVisualPoint(Position)
	local A = Instance.new("Part")
	local B = Instance.new("SelectionSphere")
	A.Anchored = true
	A.CanCollide = false
	A.Size = Vector3.new(0.001,0.001,0.001)
	A.Position = Position + Vector3.new(0,2,0)
	A.Transparency = 1
	A.Parent = VisualFolder
	A.Name = tostring(Position)
	B.Transparency = 1
	B.Parent = A
	B.Adornee = A
	B.Color3 = Color3.new(1, 0, 0.0156863)
	TweenService:Create(B,TweenI,{Transparency = 0}):Play()
end

local function checkVisibility()
	for _, player in pairs(game.Players:GetPlayers()) do
		if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
			local raycastParams = RaycastParams.new()
            raycastParams.FilterDescendantsInstances = {player.Character}
            raycastParams.FilterType = Enum.RaycastFilterType.Blacklist
            raycastParams.IgnoreWater = true
			local result = workspace:Raycast(player.Character.Head.Position, (LocalPlayer.Character.PrimaryPart.Position - player.Character.Head.Position).Unit * 70)
			if result and result.Instance:IsDescendantOf(LocalPlayer.Character) then
				return true
			end
		end
	end
	return false
end

local function CanPathTo(Position)
    local path = PathfindingService:CreatePath({
        AgentRadius = 2,
        AgentHeight = 4,
        AgentCanJump = true,
        AgentCanClimb = true
    })

    local success, errorMessage = pcall(function()
        path:ComputeAsync(Character.HumanoidRootPart.Position,Position)
    end)

	--CurrentPath = PathfindService:FindPathAsync(Character.HumanoidRootPart.Position,CoordinateFrame.Position)
	if success and path.Status == Enum.PathStatus.Success then
        for i,v in pairs(path:GetWaypoints()) do
			CreateVisualPoint(v.Position)
		end
        return true
	end
    return false
end

local function OpenDoor(DoorModel)
    
    if DoorModel.Values.Open.Value == false then

        if DoorModel.Values.CanLock.Value == true and DoorModel.Values.Locked.Value == true then
            local args = {
                [1] = "Unlock",
                [2] = DoorModel.Lock
            }
            
            DoorModel.Events.Toggle:FireServer(unpack(args)) 
            wait(0.5)  
        end

        local ClosestKnob

        if (DoorModel.Knob1.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude < (DoorModel.Knob2.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude then
            ClosestKnob = DoorModel.Knob1
        else
            ClosestKnob = DoorModel.Knob2
        end

        local args = {
            [1] = "Open",
            [2] = ClosestKnob
        }
        
        DoorModel.Events.Toggle:FireServer(unpack(args))  
        
    end

end

local function GetNearbyDoors(Range)
    local params = OverlapParams.new()
	params.FilterType = Enum.RaycastFilterType.Whitelist
	params.FilterDescendantsInstances = { workspace.Map.Doors }

    local Results = {}

	local doors = workspace:GetPartBoundsInRadius(game.Players.LocalPlayer.Character.HumanoidRootPart.Position, Range, params)
	if ( #doors > 0 ) then
		for _,v in pairs(doors) do
			if v.Parent:IsA("Model") and v.Parent:FindFirstChild("DFrame") and not table.find(Results,v.Parent) then
				table.insert(Results,v.Parent)
			end
		end
	end

    return Results
end

local function Path2CFrame(CoordinateFrame,SafeValue)
	
	CurrentlyPathing = false
	
	for i,v in pairs(VisualFolder:GetChildren()) do
		UpdateVisualPoint(v.SelectionSphere,true)
	end

	local Humanoid = Character:FindFirstChild("Humanoid")
	Controls:Disable()
	-- Method 1 - Roblox pathfinding service

    local path = PathfindingService:CreatePath({
        AgentRadius = 2,
        AgentHeight = 4,
        AgentCanJump = true,
        AgentCanClimb = true
    })

    local success, errorMessage = pcall(function()
        path:ComputeAsync(Character.HumanoidRootPart.Position,CoordinateFrame.Position)
    end)

	--CurrentPath = PathfindService:FindPathAsync(Character.HumanoidRootPart.Position,CoordinateFrame.Position)
	if success and path.Status == Enum.PathStatus.Success then
		CurrentPath = path

		CurrentlyPathing = true
		
		for i,v in pairs(CurrentPath:GetWaypoints()) do
			CreateVisualPoint(v.Position)
		end
		
		spawn(function()
			local TimesFailed = 0
            local TotalTimesFailed = 0
			while task.wait(0.5) and CurrentlyPathing == true do
				
                local NearbyDoors = GetNearbyDoors(8)

                if #NearbyDoors > 0 then
                    for i,v in pairs(NearbyDoors) do
                        if v.Values.Open.Value == false and v.Values.Broken.Value == false then
                            Humanoid.WalkToPoint = v.DFrame.Position
                            OpenDoor(v)
                        end
                    end
                end

				if TimesFailed == 2 then
                    repeat wait() until not checkVisibility()
					Debug("[!] Attempt to get unstuck failed, teleporting to next waypoint.")
					Character:PivotTo(CFrame.new(CurrentWaypoint.Position + Vector3.new(0,4,0)))
					Humanoid.WalkToPoint = CurrentWaypoint.Position
					TimesFailed = 0
				end
				
				if (Character.HumanoidRootPart.Velocity).Magnitude < 0.07 then
					Humanoid.WalkToPoint = CurrentWaypoint.Position
					task.wait(0.2)
					if (Character.HumanoidRootPart.Velocity).Magnitude < 0.07 then -- Double check
                        local targetPosition = CurrentWaypoint.Position
                        local characterPosition = game.Players.LocalPlayer.Character.PrimaryPart.Position
                        local dx = targetPosition.X - characterPosition.X
                        local dz = targetPosition.Z - characterPosition.Z
                        local distance = math.sqrt(dx*dx + dz*dz)
                        if distance < 3 and not checkVisibility() then
                            Debug("[!] Stuck, Teleporting to next waypoint.")
                            Character:PivotTo(CFrame.new(CurrentWaypoint.Position + Vector3.new(0,4,0)))
                            Humanoid.WalkToPoint = CurrentWaypoint.Position
                            TimesFailed = 0
                        else
                            TimesFailed = TimesFailed + 1
                            TotalTimesFailed = TotalTimesFailed + 1
                            Debug("[!] Stuck, attempting to jump.")
                            Humanoid.Jump = true
                            wait()
                            Humanoid.WalkToPoint = CurrentWaypoint.Position
                        end
					end
				else
					TimesFailed = 0
				end
				
			end
		end) -- should be seperate thread but keeps yielding next loop? (SOLVED, jumping cancels moveto)
		
        Pass = false

		for i,v in pairs(CurrentPath:GetWaypoints()) do

            if SafeValue.Value == true then
                Status("Safe/Register broken. Switching paths.")
                Pass = true
            end

			UpdateVisualPoint(VisualFolder[tostring(v.Position)].SelectionSphere,false,Color3.new(0.0980392, 1, 0))
			
            if not Pass then
                
                CurrentWaypoint = v
                Humanoid.WalkToPoint = v.Position

                Debug("[Debug] WalkToPoint set to ",v.Position)

                repeat task.wait() until (Character.HumanoidRootPart.Position - v.Position).Magnitude < 3.8
                
                if CurrentPath:GetWaypoints()[i+1] ~= nil and CurrentPath:GetWaypoints()[i+1].Action == Enum.PathWaypointAction.Jump then
                    task.spawn(function()
                        task.wait(0.1)
                        Humanoid.Jump = true
                    end)
                end

            end

			UpdateVisualPoint(VisualFolder[tostring(v.Position)].SelectionSphere,true)

		end
        Run(false)
        Walk(false)
		Status("Pathing complete!")
		CurrentlyPathing = false
		Controls:Enable()
        return true
	else
		CurrentlyPathing = false
		Status("[!] Method 1 failed. Utilizing fallback.")
        wait(1)
		-- Method 2 - Custom pathfinding (Slow)
        -- did not actually implement this lol, might add A* later.
        return false
	end
end

-- click to move basically ^

local function checkRaycastObstruction(startPos, endPos)
    
    local raycastParams = RaycastParams.new()
	raycastParams.FilterDescendantsInstances = {LocalPlayer.Character}
    raycastParams.FilterType = Enum.RaycastFilterType.Blacklist
    raycastParams.IgnoreWater = true
	local result = workspace:Raycast(startPos, endPos - startPos, raycastParams)

	-- Check if the raycast hit anything
	if result then
		-- Return the hit object and the hit position
		return result.Instance, result.Position
	else
		-- Return nil if the raycast did not hit anything
		return nil
	end
end

local function GetClosest(instances, origin)
    -- Initialize variables for the closest instance and distance
    local closestInstance = nil
    local closestDistance = math.huge
  
    -- Iterate through all the instances in the table
    for _, instance in pairs(instances) do
      -- Calculate the distance between the origin and the current instance
      local distance = (origin.Position - instance.PosPart.Position).magnitude
  
      -- If the distance is smaller than the current closest distance,
      -- update the closest instance and distance
      if distance < closestDistance then
        closestInstance = instance
        closestDistance = distance
      end
    end
  
    -- Return the closest instance
    return closestInstance
end

local function IsBroken(Safe)
    if Safe:FindFirstChild("Values") and Safe.Values.Broken.Value == false then
        return false
    end
    return true
end

local function LockPickSafe(Safe)
    if not IsBroken(Safe) and LocalPlayer.Character:FindFirstChild("Lockpick") or LocalPlayer.Backpack:FindFirstChild("Lockpick") then
        if Safe.Name:find("Register") then
            TTP(CFrame.new((Safe.PosPart.CFrame + Safe.PosPart.CFrame.LookVector * 2).Position,Safe.MainPart))
            local Lockpick
            if LocalPlayer.Character:FindFirstChild("Lockpick") then 
                Lockpick = LocalPlayer.Character:FindFirstChild("Lockpick")
            elseif LocalPlayer.Backpack:FindFirstChild("Lockpick") then
                LocalPlayer.Character.Humanoid:EquipTool(LocalPlayer.Backpack:FindFirstChild("Lockpick"))
                Lockpick = LocalPlayer.Character:FindFirstChild("Lockpick")
            end
            
            local args = {
                [1] = "S",
                [2] = Safe,
                [3] = "s"
            }
            
            local v34 = Lockpick.Remote:InvokeServer(unpack(args))

            wait()
            
            local args = {
                [1] = "D",
                [2] = Safe,
                [3] = "s",
                [4] = v34
            }
            
            game:GetService("Players").LocalPlayer.Character.Lockpick.Remote:InvokeServer(unpack(args))
            

            local args = {
                [1] = "C"
            }
            
            Lockpick.Remote:InvokeServer(unpack(args))
        end
    elseif not IsBroken(Safe) and LocalPlayer.Character:FindFirstChild("Crowbar") or LocalPlayer.Backpack:FindFirstChild("Crowbar") then
            TTP(CFrame.new((Safe.PosPart.CFrame + Safe.PosPart.CFrame.LookVector * 2).Position,Safe.MainPart.Position))

            LocalPlayer.Character.Humanoid:EquipTool(LocalPlayer.Backpack.Crowbar)

            repeat
                wait()
                if StaminaPercent > 50 then

                    local args = {
                        [1] = "\240\159\154\168",
                        [2] = ReplicatedStorage.Values.ServerTick.Value - 81919,
                        [3] = game:GetService("Players").LocalPlayer.Character:FindFirstChildOfClass("Tool"),
                        [4] = "DZDRRRKI",
                        [5] = Safe,
                        [6] = "Register"
                    }
                    
                    local AutoBreakSafeValue =  game:GetService("ReplicatedStorage").Events:FindFirstChild("XMHH.1"):InvokeServer(unpack(args))
    
                    wait()
    
                    local args = {
                        [1] = "\240\159\154\168",
                        [2] = tick(),
                        [3] = game:GetService("Players").LocalPlayer.Character:FindFirstChildOfClass("Tool"),
                        [4] = "2389ZFX33",
                        [5] = AutoBreakSafeValue,
                        [6] = false,
                        [7] = game:GetService("Players").LocalPlayer.Character:FindFirstChildOfClass("Tool").Handle,
                        [8] = Safe.MainPart,
                        [9] = Safe,
                        [10] = Safe.MainPart.Position,
                        [11] = Safe.MainPart.Position
                    }
                    
                    
                    game:GetService("ReplicatedStorage").Events:FindFirstChild("XMHH2.1"):FireServer(unpack(args))
                    wait(2.5)
                end

            until Safe.Values.Broken.Value == true
        end
end

local function GetClosestSafe()
    local ActiveSafes = {}
    local PathsFound = 0
    for Index,Safe in pairs(Safes:GetChildren()) do
        Status("Computing paths... " .. tostring(math.floor(100 * (Index / #Safes:GetChildren()))) .. "% | " .. tostring(PathsFound) .. " Found.")
        if (not Safe.Name:find("TO")) and IsBroken(Safe) == false and Safe.PosPart and Safe.PosPart.Position.Y > -38 and CanPathTo(Safe.PosPart.Position) then
            PathsFound = PathsFound + 1
            StatusLabel.TextColor3 = Color3.fromRGB(0, 255, 34)
            table.insert(ActiveSafes,Safe)
        end
    end
    VisualFolder:ClearAllChildren()
    StatusLabel.TextColor3 = Color3.fromRGB(252, 255, 69)
    return GetClosest(ActiveSafes,LocalPlayer.Character.HumanoidRootPart)
end

-- Main loop

task.defer(FakeAnimationHandler)


local function GetRegister(Studs)
    local Part;
    for _, v in ipairs(game:GetService("Workspace").Map.BredMakurz:GetChildren()) do
        if v:FindFirstChild("MainPart") and string.find(v.Name, "Register") and v:FindFirstChild("Values").Broken.Value == false then
            local Distance = (game:GetService("Players").LocalPlayer.Character.HumanoidRootPart.Position - v:FindFirstChild("MainPart").Position).Magnitude
            if Distance < Studs then
                Studs = Distance
                Part = v:FindFirstChild("MainPart")
            end
        end
    end
    return Part
end

local AutoBreakRegisterCoolDown = false
local CashCoolDown = false

while wait() do
    Status("Computing paths...")
    local TargetSafe = GetClosestSafe()
    if TargetSafe then
        Status("Pathfinding to " .. TargetSafe.Name)
        Path2CFrame(TargetSafe.PosPart.CFrame,TargetSafe.Values.Broken)
        
        if TargetSafe.Name:find("Register") then
            if LocalPlayer.Character:FindFirstChild("Fists") then
                local ClosestRegister = GetRegister(10)
                if ClosestRegister and not AutoBreakRegisterCoolDown then
                    AutoBreakRegisterCoolDown = true
                    TTP(CFrame.new((TargetSafe.PosPart.CFrame + Safe.PosPart.CFrame.LookVector * 2).Position,TargetSafe.MainPart.Position))
                    local AutoBreakRegisterValue = game:GetService("ReplicatedStorage").Events["XMHH.1"]:InvokeServer("\240\159\154\168", tick(), game:GetService("Players").LocalPlayer.Character:FindFirstChildOfClass("Tool"), "DZDRRRKI", ClosestRegister.Parent, "Register")
                    game:GetService("ReplicatedStorage").Events["XMHH2.1"]:FireServer("\240\159\154\168", tick(), game:GetService("Players").LocalPlayer.Character:FindFirstChildOfClass("Tool"), "2389ZFX33", AutoBreakRegisterValue, false, game:GetService("Players").LocalPlayer.Character["Right Arm"], ClosestRegister, ClosestRegister.Parent, ClosestRegister.Position, ClosestRegister.Position)
                    wait(0.5)
                    AutoBreakRegisterCoolDown = false
                end
            else
                LocalPlayer.Character.Humanoid:EquipTool(LocalPlayer.Backpack.Fists)
                local ClosestRegister = GetRegister(10)
                if ClosestRegister and not AutoBreakRegisterCoolDown then
                    AutoBreakRegisterCoolDown = true
                    local AutoBreakRegisterValue = game:GetService("ReplicatedStorage").Events["XMHH.1"]:InvokeServer("\240\159\154\168", tick(), game:GetService("Players").LocalPlayer.Character:FindFirstChildOfClass("Tool"), "DZDRRRKI", ClosestRegister.Parent, "Register")
                    game:GetService("ReplicatedStorage").Events["XMHH2.1"]:FireServer("\240\159\154\168", tick(), game:GetService("Players").LocalPlayer.Character:FindFirstChildOfClass("Tool"), "2389ZFX33", AutoBreakRegisterValue, false, game:GetService("Players").LocalPlayer.Character["Right Arm"], ClosestRegister, ClosestRegister.Parent, ClosestRegister.Position, ClosestRegister.Position)
                    wait(0.5)
                    AutoBreakRegisterCoolDown = false
                end
            end
            wait(2)
            local CashToGet = {}
            for i, v in pairs(game:GetService("Workspace").Filter.SpawnedBread:GetChildren()) do
                if game:GetService("Players").LocalPlayer.Character.Humanoid.Health > 0 then
                    if (TargetSafe.PosPart.Position - v.Position).Magnitude < 20 then
                        table.insert(CashToGet,v)
                    end
                end
            end
            for i, v in pairs(CashToGet) do
                if game:GetService("Players").LocalPlayer.Character.Humanoid.Health > 0 then
                    game:GetService("ReplicatedStorage").Events.CZDPZUS:FireServer(v)
                    wait(1.5)
                end
            end
            game:GetService("Players").LocalPlayer.Character.Humanoid:UnequipTools()
        else
            LockPickSafe(TargetSafe)
            wait(2)
            local CashToGet = {}
            for i, v in pairs(game:GetService("Workspace").Filter.SpawnedBread:GetChildren()) do
                if game:GetService("Players").LocalPlayer.Character.Humanoid.Health > 0 then
                    if (TargetSafe.PosPart.Position - v.Position).Magnitude < 20 then
                        table.insert(CashToGet,v)
                    end
                end
            end
            for i, v in pairs(CashToGet) do
                if game:GetService("Players").LocalPlayer.Character.Humanoid.Health > 0 then
                    game:GetService("ReplicatedStorage").Events.CZDPZUS:FireServer(v)
                    wait(1.5)
                end
            end
            game:GetService("Players").LocalPlayer.Character.Humanoid:UnequipTools()
        end
    end
end








-- task.spawn(function()
--     while wait() do
--         if LocalPlayer.Character:FindFirstChild("Fists") then
--             local ClosestRegister = GetRegister(10)
--             if ClosestRegister and not AutoBreakRegisterCoolDown then
--                 AutoBreakRegisterCoolDown = true
--                 local AutoBreakRegisterValue = game:GetService("ReplicatedStorage").Events["XMHH.1"]:InvokeServer("\240\159\154\168", tick(), game:GetService("Players").LocalPlayer.Character:FindFirstChildOfClass("Tool"), "DZDRRRKI", ClosestRegister.Parent, "Register")
--                 game:GetService("ReplicatedStorage").Events["XMHH2.1"]:FireServer("\240\159\154\168", tick(), game:GetService("Players").LocalPlayer.Character:FindFirstChildOfClass("Tool"), "2389ZFX33", AutoBreakRegisterValue, false, game:GetService("Players").LocalPlayer.Character["Right Arm"], ClosestRegister, ClosestRegister.Parent, ClosestRegister.Position, ClosestRegister.Position)
--                 wait(0.5)
--                 AutoBreakRegisterCoolDown = false
--             end
--         end
--     end
-- end)
