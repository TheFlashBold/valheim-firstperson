# Valheim Firstperson
It's a simple mod to make the valheim live much better.

# Release
[v0.3](https://github.com/TheFlashBold/valheim-firstperson/releases/tag/0.3)
```
+ fov zooming with V
+ m_dataPerSec = 245760
+ firstperson toggle with NUMPAD_ENTER
```

# Code
Use a disassembler like https://github.com/dnSpy/dnSpy.

vahleim_assembly.dll > GameCamera
```C#
using System;
using System.IO;
using UnityEngine;

public partial class GameCamera : MonoBehaviour
{
	// Token: 0x06000B67 RID: 2919
	private void LateUpdate()
	{
		if (Input.GetKeyDown(KeyCode.KeypadEnter))
		{
			this.isFirstPerson = !this.isFirstPerson;
			if (this.m_camera.nearClipPlane != this.m_nearClipPlaneMin)
			{
				this.m_camera.nearClipPlane = this.m_nearClipPlaneMin;
			}
		}
		if (Input.GetKeyDown(KeyCode.V))
		{
			this.m_fov = this.m_camera.fieldOfView;
		}
		if (Input.GetKey(KeyCode.V))
		{
			this.m_camera.fieldOfView = Mathf.Lerp(this.m_camera.fieldOfView, 20f, Time.deltaTime);
			this.m_skyCamera.fieldOfView = Mathf.Lerp(this.m_camera.fieldOfView, 20f, Time.deltaTime);
		}
		if (!Input.GetKey(KeyCode.V))
		{
			this.m_camera.fieldOfView = this.m_fov;
			this.m_skyCamera.fieldOfView = this.m_fov;
		}
		float deltaTime = Time.deltaTime;
		if (Input.GetKeyDown(KeyCode.F11) || (this.m_freeFly && Input.GetKeyDown(KeyCode.Mouse1)))
		{
			GameCamera.ScreenShot();
		}
		Player localPlayer = Player.m_localPlayer;
		if (localPlayer)
		{
			if (this.isFirstPerson && Player.m_localPlayer != null && !Player.m_localPlayer.IsDead())
			{
				base.transform.position = localPlayer.GetEyePoint();
				base.transform.rotation = localPlayer.m_eye.transform.rotation;
				this.UpdateCameraShake(Time.deltaTime);
			}
			else
			{
				this.UpdateBaseOffset(localPlayer, deltaTime);
				this.UpdateCamera(deltaTime);
			}
		}
		this.UpdateMouseCapture();
		this.UpdateListner();
	}

	// Token: 0x06000B6B RID: 2923 RVA: 0x00052720 File Offset: 0x00050920
	private void UpdateCamera(float dt)
	{
		if (this.m_freeFly)
		{
			this.UpdateFreeFly(dt);
			this.UpdateCameraShake(dt);
			return;
		}
		Player localPlayer = Player.m_localPlayer;
		if (localPlayer)
		{
			if ((!Chat.instance || !Chat.instance.HasFocus()) && !global::Console.IsVisible() && !InventoryGui.IsVisible() && !StoreGui.IsVisible() && !Menu.IsVisible() && !Minimap.IsOpen() && !localPlayer.InCutscene() && !localPlayer.InPlaceMode())
			{
				float minDistance = this.m_minDistance;
				float axis = Input.GetAxis("Mouse ScrollWheel");
				this.m_distance -= axis * this.m_zoomSens;
				float max = (localPlayer.GetControlledShip() != null) ? this.m_maxDistanceBoat : this.m_maxDistance;
				this.m_distance = Mathf.Clamp(this.m_distance, minDistance, max);
			}
			if (localPlayer.IsDead() && localPlayer.GetRagdoll())
			{
				Vector3 averageBodyPosition = localPlayer.GetRagdoll().GetAverageBodyPosition();
				base.transform.LookAt(averageBodyPosition);
			}
			else
			{
				Vector3 position;
				Quaternion rotation;
				this.GetCameraPosition(dt, out position, out rotation);
				base.transform.position = position;
				base.transform.rotation = rotation;
			}
			this.UpdateCameraShake(dt);
		}
	}

	// Token: 0x04000CAE RID: 3246
	private bool isFirstPerson;
}
```

vahleim_assembly.dll > ZDOMan
```C#
// Token: 0x020000CC RID: 204
public partial class ZDOMan
{
	// Token: 0x04000A17 RID: 2583
	private int m_dataPerSec = 245760;
}
```
