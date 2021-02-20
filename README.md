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

vahleim_assembly.dll > GameCamera > LateUpdate()
```C#
// GameCamera
// Token: 0x06000B67 RID: 2919 RVA: 0x00052488 File Offset: 0x00050688
private bool isFirstPerson;

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
```
